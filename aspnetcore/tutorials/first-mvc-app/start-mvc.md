---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 0b28e99d9c56d92e157e9010880e58ec5165196a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774991"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="8bcaa-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="8bcaa-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="8bcaa-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8bcaa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8bcaa-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8bcaa-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="8bcaa-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8bcaa-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-108">Create a web app.</span></span>
> * <span data-ttu-id="8bcaa-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8bcaa-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-110">Work with a database.</span></span>
> * <span data-ttu-id="8bcaa-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-111">Add search and validation.</span></span>

<span data-ttu-id="8bcaa-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8bcaa-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8bcaa-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="8bcaa-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="8bcaa-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8bcaa-119">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="8bcaa-120">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8bcaa-122">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="8bcaa-123">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="8bcaa-125">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="8bcaa-126">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="8bcaa-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="8bcaa-127">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8bcaa-128">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8bcaa-129">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8bcaa-131">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8bcaa-132">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="8bcaa-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8bcaa-133">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8bcaa-134">Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8bcaa-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8bcaa-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="8bcaa-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8bcaa-137">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="8bcaa-137">Select **Yes**</span></span>

  * <span data-ttu-id="8bcaa-138">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8bcaa-139">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8bcaa-141">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-141">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8bcaa-143">**Daha sonra** **.NET Core** > **App** > **Web uygulaması (Model-View-Controller)** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="8bcaa-145">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **.NET Core 3,1**'nin **hedef çerçevesini** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,1 seçimi](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="8bcaa-147">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="8bcaa-148">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="8bcaa-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8bcaa-150">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8bcaa-151">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8bcaa-152">Adres çubuğunda `localhost:port#` , gibi `example.com`bir şey gösterilmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-153">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8bcaa-154">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8bcaa-155">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8bcaa-156">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8bcaa-157">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8bcaa-159">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="8bcaa-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="8bcaa-161">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-161">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8bcaa-164">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8bcaa-165">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8bcaa-166">Adres çubuğu gibi `example.com`bir `localhost:port:5001` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-167">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8bcaa-168">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8bcaa-169">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8bcaa-170">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-172">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8bcaa-173">Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="8bcaa-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8bcaa-174">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve `http://localhost:port`' a gider ve *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8bcaa-175">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-176">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8bcaa-177">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8bcaa-178">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8bcaa-179">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="8bcaa-180">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-180">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8bcaa-182">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8bcaa-183">Sonraki</span><span class="sxs-lookup"><span data-stu-id="8bcaa-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="8bcaa-184">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="8bcaa-185">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="8bcaa-186">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8bcaa-187">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-187">Create a web app.</span></span>
> * <span data-ttu-id="8bcaa-188">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="8bcaa-189">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-189">Work with a database.</span></span>
> * <span data-ttu-id="8bcaa-190">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-190">Add search and validation.</span></span>

<span data-ttu-id="8bcaa-191">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="8bcaa-192">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="8bcaa-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-195">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="8bcaa-196">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="8bcaa-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8bcaa-198">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="8bcaa-199">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="8bcaa-201">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="8bcaa-202">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="8bcaa-204">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="8bcaa-205">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="8bcaa-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="8bcaa-206">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="8bcaa-207">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="8bcaa-208">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8bcaa-210">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="8bcaa-211">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="8bcaa-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="8bcaa-212">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="8bcaa-213">Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="8bcaa-214">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="8bcaa-215">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="8bcaa-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="8bcaa-216">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="8bcaa-216">Select **Yes**</span></span>

  * <span data-ttu-id="8bcaa-217">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="8bcaa-218">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-219">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8bcaa-220">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-220">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8bcaa-222">**Daha sonra** **.NET Core** > **App** > **Web uygulaması (Model-View-Controller)** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="8bcaa-224">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **.NET Core 2,2**'nin varsayılan **hedef çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 seçimi](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="8bcaa-226">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="8bcaa-227">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="8bcaa-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8bcaa-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8bcaa-229">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="8bcaa-230">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8bcaa-231">Adres çubuğunda `localhost:port#` , gibi `example.com`bir şey gösterilmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-232">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8bcaa-233">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="8bcaa-234">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8bcaa-235">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="8bcaa-236">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="8bcaa-238">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="8bcaa-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="8bcaa-240">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8bcaa-241">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-241">This app doesn't track personal information.</span></span> <span data-ttu-id="8bcaa-242">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="8bcaa-244">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-244">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8bcaa-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8bcaa-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8bcaa-247">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="8bcaa-248">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="8bcaa-249">Adres çubuğu gibi `example.com`bir `localhost:port:5001` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-250">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="8bcaa-251">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="8bcaa-252">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8bcaa-253">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="8bcaa-254">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8bcaa-255">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-255">This app doesn't track personal information.</span></span> <span data-ttu-id="8bcaa-256">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="8bcaa-258">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-258">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8bcaa-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8bcaa-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8bcaa-261">Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="8bcaa-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="8bcaa-262">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve `http://localhost:port`' a gider ve *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8bcaa-263">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8bcaa-264">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="8bcaa-265">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="8bcaa-266">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="8bcaa-267">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="8bcaa-268">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="8bcaa-269">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-269">This app doesn't track personal information.</span></span> <span data-ttu-id="8bcaa-270">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="8bcaa-272">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8bcaa-272">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="8bcaa-274">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8bcaa-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8bcaa-275">Sonraki</span><span class="sxs-lookup"><span data-stu-id="8bcaa-275">Next</span></span>](adding-controller.md)

::: moniker-end
