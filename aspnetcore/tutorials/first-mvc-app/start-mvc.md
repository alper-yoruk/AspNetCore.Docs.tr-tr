---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213088"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="c0c5a-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c0c5a-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="c0c5a-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0c5a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c0c5a-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c0c5a-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="c0c5a-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c0c5a-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-108">Create a web app.</span></span>
> * <span data-ttu-id="c0c5a-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c0c5a-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-110">Work with a database.</span></span>
> * <span data-ttu-id="c0c5a-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-111">Add search and validation.</span></span>

<span data-ttu-id="c0c5a-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c0c5a-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="c0c5a-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="c0c5a-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0c5a-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0c5a-119">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c0c5a-120">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c0c5a-122">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c0c5a-123">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="c0c5a-125">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c0c5a-126">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c0c5a-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="c0c5a-127">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c0c5a-128">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c0c5a-129">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c0c5a-131">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c0c5a-132">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c0c5a-133">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c0c5a-134">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c0c5a-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c0c5a-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="c0c5a-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c0c5a-137">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="c0c5a-137">Select **Yes**</span></span>

  * <span data-ttu-id="c0c5a-138">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c0c5a-139">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c0c5a-141">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-141">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c0c5a-143">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c0c5a-144">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c0c5a-146">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c0c5a-147">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c0c5a-148">**Hedef çerçeve**seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="c0c5a-149">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-149">Select **Next**.</span></span>

* <span data-ttu-id="c0c5a-150">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="c0c5a-152">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c0c5a-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c0c5a-154">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c0c5a-155">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c0c5a-156">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-157">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c0c5a-158">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c0c5a-159">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c0c5a-160">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c0c5a-161">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c0c5a-163">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="c0c5a-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="c0c5a-165">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-165">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c0c5a-168">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c0c5a-169">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c0c5a-170">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-171">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c0c5a-172">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c0c5a-173">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c0c5a-174">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-176">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c0c5a-177">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c0c5a-178">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c0c5a-179">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-180">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c0c5a-181">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c0c5a-182">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c0c5a-183">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="c0c5a-184">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-184">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c0c5a-186">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c0c5a-187">Sonraki</span><span class="sxs-lookup"><span data-stu-id="c0c5a-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c0c5a-188">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c0c5a-189">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="c0c5a-190">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c0c5a-191">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-191">Create a web app.</span></span>
> * <span data-ttu-id="c0c5a-192">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c0c5a-193">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-193">Work with a database.</span></span>
> * <span data-ttu-id="c0c5a-194">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-194">Add search and validation.</span></span>

<span data-ttu-id="c0c5a-195">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c0c5a-196">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="c0c5a-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="c0c5a-200">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0c5a-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0c5a-202">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c0c5a-203">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c0c5a-205">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c0c5a-206">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="c0c5a-208">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c0c5a-209">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c0c5a-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="c0c5a-210">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c0c5a-211">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c0c5a-212">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c0c5a-214">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c0c5a-215">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c0c5a-216">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c0c5a-217">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c0c5a-218">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c0c5a-219">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="c0c5a-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c0c5a-220">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="c0c5a-220">Select **Yes**</span></span>

  * <span data-ttu-id="c0c5a-221">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c0c5a-222">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-223">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c0c5a-224">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-224">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c0c5a-226">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c0c5a-227">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="c0c5a-228">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="c0c5a-229">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="c0c5a-230">**Hedef çerçeve**seçme seçeneği sunulursa, en son 2. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="c0c5a-231">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-231">Select **Next**.</span></span>

* <span data-ttu-id="c0c5a-232">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="c0c5a-233">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c0c5a-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0c5a-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c0c5a-235">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c0c5a-236">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c0c5a-237">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-238">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c0c5a-239">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c0c5a-240">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c0c5a-241">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c0c5a-242">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c0c5a-244">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="c0c5a-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="c0c5a-246">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c0c5a-247">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-247">This app doesn't track personal information.</span></span> <span data-ttu-id="c0c5a-248">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="c0c5a-250">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-250">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0c5a-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0c5a-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c0c5a-253">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c0c5a-254">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c0c5a-255">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-256">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c0c5a-257">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c0c5a-258">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c0c5a-259">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="c0c5a-260">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c0c5a-261">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-261">This app doesn't track personal information.</span></span> <span data-ttu-id="c0c5a-262">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="c0c5a-264">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-264">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0c5a-266">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0c5a-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c0c5a-267">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c0c5a-268">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c0c5a-269">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c0c5a-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c0c5a-270">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c0c5a-271">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c0c5a-272">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c0c5a-273">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="c0c5a-274">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c0c5a-275">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-275">This app doesn't track personal information.</span></span> <span data-ttu-id="c0c5a-276">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="c0c5a-278">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c0c5a-278">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c0c5a-280">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c0c5a-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c0c5a-281">Sonraki</span><span class="sxs-lookup"><span data-stu-id="c0c5a-281">Next</span></span>](adding-controller.md)

::: moniker-end
