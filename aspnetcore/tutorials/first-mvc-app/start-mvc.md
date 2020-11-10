---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 11/16/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422765"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="2db8c-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="2db8c-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="2db8c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2db8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2db8c-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2db8c-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="2db8c-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2db8c-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2db8c-108">Create a web app.</span></span>
> * <span data-ttu-id="2db8c-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2db8c-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-110">Work with a database.</span></span>
> * <span data-ttu-id="2db8c-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-111">Add search and validation.</span></span>

<span data-ttu-id="2db8c-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2db8c-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2db8c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="2db8c-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="2db8c-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2db8c-119">Visual Studio 'Yu başlatın ve **Yeni proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="2db8c-120">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="2db8c-121">**Yeni projenizi yapılandırın** Iletişim kutusunda `MvcMovie` **Proje adı** ' nı girin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="2db8c-122">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="2db8c-123">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-123">Select **Create**.</span></span>
1. <span data-ttu-id="2db8c-124">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:</span><span class="sxs-lookup"><span data-stu-id="2db8c-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="2db8c-125">**.NET Core** ve **ASP.NET Core 5,0** açılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="2db8c-126">**ASP.NET Core Web uygulaması (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="2db8c-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="2db8c-127">**Oluştur**</span><span class="sxs-lookup"><span data-stu-id="2db8c-127">**Create**</span></span>

![<span data-ttu-id="2db8c-128">Yeni bir ASP.NET Core Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="2db8c-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/5/mvc.png)

<span data-ttu-id="2db8c-129">Projeyi oluşturmaya yönelik alternatif yaklaşımlar için bkz. [Visual Studio 'da yeni proje oluşturma](/visualstudio/ide/create-new-project).</span><span class="sxs-lookup"><span data-stu-id="2db8c-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="2db8c-130">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="2db8c-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2db8c-131">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="2db8c-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2db8c-132">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="2db8c-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-134">Öğreticide VS Code anladığı varsayılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="2db8c-135">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="2db8c-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2db8c-136">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2db8c-137">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2db8c-138">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2db8c-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2db8c-139">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="2db8c-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2db8c-140">**Evet** ’i seçin</span><span class="sxs-lookup"><span data-stu-id="2db8c-140">Select **Yes**</span></span>

  * <span data-ttu-id="2db8c-141">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2db8c-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2db8c-142">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="2db8c-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-143">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2db8c-144">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-144">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2db8c-146">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)** ' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2db8c-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="2db8c-147">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="2db8c-149">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2db8c-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="2db8c-150">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="2db8c-151">**Hedef çerçeve** seçme seçeneği sunulursa, en son 5. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-151">If presented an option to select a **Target Framework** , select the latest 5.x version.</span></span>

  <span data-ttu-id="2db8c-152">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-152">Select **Next**.</span></span>

* <span data-ttu-id="2db8c-153">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-153">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="2db8c-155">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="2db8c-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2db8c-157">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2db8c-158">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2db8c-159">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-160">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-161">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2db8c-162">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-163">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2db8c-164">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2db8c-166">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="2db8c-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="2db8c-168">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-168">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-171">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2db8c-172">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2db8c-173">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-174">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2db8c-175">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2db8c-176">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-177">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-179">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2db8c-180">**Run**  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2db8c-181">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2db8c-182">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-183">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-184">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2db8c-185">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2db8c-186">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="2db8c-187">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-187">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2db8c-189">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2db8c-190">Sonraki</span><span class="sxs-lookup"><span data-stu-id="2db8c-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2db8c-191">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2db8c-192">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="2db8c-193">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2db8c-194">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2db8c-194">Create a web app.</span></span>
> * <span data-ttu-id="2db8c-195">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2db8c-196">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-196">Work with a database.</span></span>
> * <span data-ttu-id="2db8c-197">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-197">Add search and validation.</span></span>

<span data-ttu-id="2db8c-198">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2db8c-199">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2db8c-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-202">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="2db8c-203">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="2db8c-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2db8c-205">Visual Studio 'da **Yeni proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2db8c-206">**ASP.NET Core Web uygulaması** > **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2db8c-208">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2db8c-209">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="2db8c-211">**Web uygulaması (Model-View-Controller)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="2db8c-212">Açılan kutularda **.NET Core** ve **ASP.NET Core 3,1** ' i seçin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="2db8c-213">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="2db8c-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="2db8c-214">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="2db8c-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2db8c-215">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="2db8c-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2db8c-216">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="2db8c-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-218">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2db8c-219">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="2db8c-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2db8c-220">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2db8c-221">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2db8c-222">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2db8c-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2db8c-223">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="2db8c-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2db8c-224">**Evet** ’i seçin</span><span class="sxs-lookup"><span data-stu-id="2db8c-224">Select **Yes**</span></span>

  * <span data-ttu-id="2db8c-225">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2db8c-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2db8c-226">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="2db8c-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-227">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2db8c-228">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-228">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2db8c-230">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)** ' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2db8c-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="2db8c-231">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="2db8c-233">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2db8c-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="2db8c-234">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="2db8c-235">**Hedef çerçeve** seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-235">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="2db8c-236">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-236">Select **Next**.</span></span>

* <span data-ttu-id="2db8c-237">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-237">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="2db8c-239">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="2db8c-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2db8c-241">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2db8c-242">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2db8c-243">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-244">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-245">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2db8c-246">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-247">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2db8c-248">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2db8c-250">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="2db8c-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="2db8c-252">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-252">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-255">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2db8c-256">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2db8c-257">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-258">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2db8c-259">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2db8c-260">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-261">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-263">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2db8c-264">**Run**  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2db8c-265">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2db8c-266">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-267">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-268">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2db8c-269">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2db8c-270">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="2db8c-271">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-271">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2db8c-273">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2db8c-274">Sonraki</span><span class="sxs-lookup"><span data-stu-id="2db8c-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2db8c-275">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2db8c-276">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="2db8c-277">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2db8c-278">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2db8c-278">Create a web app.</span></span>
> * <span data-ttu-id="2db8c-279">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2db8c-280">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-280">Work with a database.</span></span>
> * <span data-ttu-id="2db8c-281">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-281">Add search and validation.</span></span>

<span data-ttu-id="2db8c-282">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2db8c-283">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="2db8c-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-286">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="2db8c-287">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="2db8c-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2db8c-289">Visual Studio 'da **Yeni proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2db8c-290">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2db8c-292">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2db8c-293">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="2db8c-295">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-295">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="2db8c-296">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="2db8c-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="2db8c-297">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="2db8c-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2db8c-298">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="2db8c-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2db8c-299">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-301">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2db8c-302">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="2db8c-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2db8c-303">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2db8c-304">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2db8c-305">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2db8c-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2db8c-306">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="2db8c-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2db8c-307">**Evet** ’i seçin</span><span class="sxs-lookup"><span data-stu-id="2db8c-307">Select **Yes**</span></span>

  * <span data-ttu-id="2db8c-308">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2db8c-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2db8c-309">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="2db8c-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-310">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2db8c-311">**Dosya** > **yeni çözüm** ' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-311">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2db8c-313">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)** ' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="2db8c-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="2db8c-314">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="2db8c-315">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2db8c-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="2db8c-316">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="2db8c-317">**Hedef çerçeve** seçme seçeneği sunulursa, en son 2. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-317">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="2db8c-318">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-318">Select **Next**.</span></span>

* <span data-ttu-id="2db8c-319">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-319">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2db8c-320">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="2db8c-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2db8c-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2db8c-322">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2db8c-323">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2db8c-324">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-325">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-326">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2db8c-327">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-328">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2db8c-329">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2db8c-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2db8c-331">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="2db8c-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="2db8c-333">İzlemeye onay vermek için **Kabul Et** 'i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2db8c-334">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="2db8c-334">This app doesn't track personal information.</span></span> <span data-ttu-id="2db8c-335">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="2db8c-337">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-337">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2db8c-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2db8c-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2db8c-340">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="2db8c-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2db8c-341">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2db8c-342">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-343">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2db8c-344">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2db8c-345">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2db8c-346">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="2db8c-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="2db8c-347">İzlemeye onay vermek için **Kabul Et** 'i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2db8c-348">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="2db8c-348">This app doesn't track personal information.</span></span> <span data-ttu-id="2db8c-349">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="2db8c-351">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-351">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2db8c-353">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2db8c-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2db8c-354">**Run**  >  Uygulamayı başlatmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2db8c-355">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2db8c-356">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="2db8c-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2db8c-357">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2db8c-358">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2db8c-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2db8c-359">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2db8c-360">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="2db8c-361">İzlemeye onay vermek için **Kabul Et** 'i seçin.</span><span class="sxs-lookup"><span data-stu-id="2db8c-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2db8c-362">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="2db8c-362">This app doesn't track personal information.</span></span> <span data-ttu-id="2db8c-363">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="2db8c-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="2db8c-365">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2db8c-365">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2db8c-367">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2db8c-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2db8c-368">Sonraki</span><span class="sxs-lookup"><span data-stu-id="2db8c-368">Next</span></span>](adding-controller.md)

::: moniker-end
