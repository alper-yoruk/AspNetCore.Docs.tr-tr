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
ms.openlocfilehash: 01321d68defafbe79371250669f921307bcfdba6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407050"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="c41e5-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c41e5-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="c41e5-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c41e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c41e5-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c41e5-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="c41e5-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c41e5-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c41e5-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c41e5-108">Create a web app.</span></span>
> * <span data-ttu-id="c41e5-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c41e5-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-110">Work with a database.</span></span>
> * <span data-ttu-id="c41e5-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-111">Add search and validation.</span></span>

<span data-ttu-id="c41e5-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c41e5-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c41e5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="c41e5-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c41e5-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c41e5-119">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c41e5-120">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c41e5-122">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c41e5-123">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="c41e5-125">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c41e5-126">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c41e5-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="c41e5-127">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="c41e5-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c41e5-128">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="c41e5-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c41e5-129">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="c41e5-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c41e5-131">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c41e5-132">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c41e5-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c41e5-133">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c41e5-134">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c41e5-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c41e5-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c41e5-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="c41e5-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c41e5-137">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="c41e5-137">Select **Yes**</span></span>

  * <span data-ttu-id="c41e5-138">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c41e5-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c41e5-139">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="c41e5-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c41e5-141">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-141">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c41e5-143">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="c41e5-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c41e5-144">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="c41e5-146">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="c41e5-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="c41e5-147">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="c41e5-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="c41e5-148">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="c41e5-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="c41e5-149">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-149">Select **Next**.</span></span>

  ![macOS .NET Core 3,1 seçimi](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="c41e5-151">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="c41e5-153">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c41e5-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c41e5-155">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c41e5-156">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c41e5-157">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-158">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c41e5-159">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c41e5-160">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c41e5-161">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c41e5-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c41e5-162">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c41e5-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c41e5-164">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="c41e5-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="c41e5-166">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c41e5-166">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c41e5-169">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c41e5-170">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c41e5-171">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-172">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c41e5-173">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c41e5-174">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c41e5-175">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c41e5-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-177">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c41e5-178">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c41e5-179">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c41e5-180">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-181">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c41e5-182">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c41e5-183">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c41e5-184">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="c41e5-185">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c41e5-185">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c41e5-187">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c41e5-188">Sonraki</span><span class="sxs-lookup"><span data-stu-id="c41e5-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="c41e5-189">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="c41e5-190">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="c41e5-191">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c41e5-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c41e5-192">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c41e5-192">Create a web app.</span></span>
> * <span data-ttu-id="c41e5-193">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="c41e5-194">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-194">Work with a database.</span></span>
> * <span data-ttu-id="c41e5-195">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-195">Add search and validation.</span></span>

<span data-ttu-id="c41e5-196">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="c41e5-197">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="c41e5-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-200">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="c41e5-201">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c41e5-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c41e5-203">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="c41e5-204">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="c41e5-206">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="c41e5-207">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="c41e5-209">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="c41e5-210">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="c41e5-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="c41e5-211">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="c41e5-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="c41e5-212">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="c41e5-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="c41e5-213">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c41e5-215">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="c41e5-216">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="c41e5-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="c41e5-217">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="c41e5-218">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="c41e5-219">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c41e5-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="c41e5-220">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="c41e5-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="c41e5-221">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="c41e5-221">Select **Yes**</span></span>

  * <span data-ttu-id="c41e5-222">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c41e5-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="c41e5-223">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="c41e5-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-224">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c41e5-225">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-225">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="c41e5-227">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="c41e5-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="c41e5-228">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="c41e5-229">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **.NET Core 2,2**'nin varsayılan **hedef çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 seçimi](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="c41e5-231">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="c41e5-232">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="c41e5-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c41e5-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c41e5-234">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="c41e5-235">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="c41e5-236">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-237">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c41e5-238">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="c41e5-239">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c41e5-240">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c41e5-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="c41e5-241">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c41e5-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="c41e5-243">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="c41e5-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="c41e5-245">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c41e5-246">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c41e5-246">This app doesn't track personal information.</span></span> <span data-ttu-id="c41e5-247">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="c41e5-249">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c41e5-249">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c41e5-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c41e5-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c41e5-252">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="c41e5-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="c41e5-253">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="c41e5-254">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-255">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="c41e5-256">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="c41e5-257">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="c41e5-258">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c41e5-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="c41e5-259">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c41e5-260">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c41e5-260">This app doesn't track personal information.</span></span> <span data-ttu-id="c41e5-261">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="c41e5-263">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c41e5-263">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c41e5-265">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c41e5-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c41e5-266">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="c41e5-267">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="c41e5-268">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="c41e5-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="c41e5-269">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="c41e5-270">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c41e5-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="c41e5-271">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="c41e5-272">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="c41e5-273">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c41e5-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="c41e5-274">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="c41e5-274">This app doesn't track personal information.</span></span> <span data-ttu-id="c41e5-275">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="c41e5-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="c41e5-277">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c41e5-277">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="c41e5-279">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c41e5-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="c41e5-280">Sonraki</span><span class="sxs-lookup"><span data-stu-id="c41e5-280">Next</span></span>](adding-controller.md)

::: moniker-end
