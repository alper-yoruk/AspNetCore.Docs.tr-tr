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
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="f796e-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="f796e-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="f796e-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f796e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f796e-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="f796e-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f796e-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="f796e-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="f796e-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f796e-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f796e-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f796e-108">Create a web app.</span></span>
> * <span data-ttu-id="f796e-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="f796e-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f796e-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="f796e-110">Work with a database.</span></span>
> * <span data-ttu-id="f796e-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f796e-111">Add search and validation.</span></span>

<span data-ttu-id="f796e-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="f796e-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f796e-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f796e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="f796e-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="f796e-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f796e-119">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f796e-120">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f796e-122">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f796e-123">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="f796e-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="f796e-125">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f796e-126">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f796e-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="f796e-127">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="f796e-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f796e-128">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="f796e-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f796e-129">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="f796e-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f796e-131">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f796e-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f796e-132">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="f796e-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f796e-133">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="f796e-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f796e-134">Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f796e-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f796e-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f796e-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f796e-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="f796e-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f796e-137">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="f796e-137">Select **Yes**</span></span>

  * <span data-ttu-id="f796e-138">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f796e-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f796e-139">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="f796e-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f796e-141">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-141">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f796e-143">**Daha sonra** **.NET Core** > **App** > **Web uygulaması (Model-View-Controller)** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f796e-145">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **.NET Core 3,1**'nin **hedef çerçevesini** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f796e-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3,1 seçimi](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="f796e-147">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f796e-148">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f796e-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f796e-150">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f796e-151">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f796e-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f796e-152">Adres çubuğunda `localhost:port#` , gibi `example.com`bir şey gösterilmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f796e-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-153">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f796e-154">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f796e-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f796e-155">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f796e-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f796e-156">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f796e-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f796e-157">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f796e-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f796e-159">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="f796e-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="f796e-161">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f796e-161">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f796e-164">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f796e-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f796e-165">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f796e-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f796e-166">Adres çubuğu gibi `example.com`bir `localhost:port:5001` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="f796e-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-167">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f796e-168">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="f796e-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f796e-169">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f796e-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f796e-170">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f796e-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-172">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f796e-173">Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="f796e-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f796e-174">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve `http://localhost:port`' a gider ve *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f796e-175">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="f796e-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-176">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f796e-177">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f796e-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f796e-178">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="f796e-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f796e-179">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f796e-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="f796e-180">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f796e-180">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f796e-182">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f796e-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f796e-183">Sonraki</span><span class="sxs-lookup"><span data-stu-id="f796e-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="f796e-184">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="f796e-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f796e-185">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="f796e-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="f796e-186">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f796e-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f796e-187">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f796e-187">Create a web app.</span></span>
> * <span data-ttu-id="f796e-188">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="f796e-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="f796e-189">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="f796e-189">Work with a database.</span></span>
> * <span data-ttu-id="f796e-190">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f796e-190">Add search and validation.</span></span>

<span data-ttu-id="f796e-191">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="f796e-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="f796e-192">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="f796e-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-195">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="f796e-196">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="f796e-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f796e-198">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="f796e-199">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="f796e-201">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="f796e-202">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="f796e-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="f796e-204">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="f796e-205">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="f796e-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="f796e-206">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="f796e-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="f796e-207">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="f796e-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="f796e-208">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="f796e-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f796e-210">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="f796e-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="f796e-211">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="f796e-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="f796e-212">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="f796e-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f796e-213">Dizinleri (`cd`), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f796e-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="f796e-214">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f796e-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="f796e-215">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="f796e-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="f796e-216">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="f796e-216">Select **Yes**</span></span>

  * <span data-ttu-id="f796e-217">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f796e-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="f796e-218">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="f796e-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-219">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f796e-220">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-220">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="f796e-222">**Daha sonra** **.NET Core** > **App** > **Web uygulaması (Model-View-Controller)** > öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="f796e-224">**Yeni ASP.NET Core Web API 'Nizi yapılandırın** iletişim kutusunda, **.NET Core 2,2**'nin varsayılan **hedef çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="f796e-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2,2 seçimi](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="f796e-226">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="f796e-227">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f796e-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f796e-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f796e-229">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="f796e-230">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f796e-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="f796e-231">Adres çubuğunda `localhost:port#` , gibi `example.com`bir şey gösterilmediğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f796e-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-232">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f796e-233">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f796e-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="f796e-234">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f796e-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f796e-235">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f796e-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="f796e-236">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f796e-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="f796e-238">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="f796e-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="f796e-240">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f796e-241">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="f796e-241">This app doesn't track personal information.</span></span> <span data-ttu-id="f796e-242">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="f796e-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="f796e-244">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f796e-244">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f796e-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f796e-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f796e-247">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="f796e-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="f796e-248">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="f796e-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="f796e-249">Adres çubuğu gibi `example.com`bir `localhost:port:5001` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="f796e-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-250">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="f796e-251">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="f796e-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="f796e-252">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f796e-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="f796e-253">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="f796e-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="f796e-254">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f796e-255">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="f796e-255">This app doesn't track personal information.</span></span> <span data-ttu-id="f796e-256">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="f796e-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="f796e-258">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f796e-258">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f796e-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f796e-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f796e-261">Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="f796e-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="f796e-262">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve `http://localhost:port`' a gider ve *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="f796e-263">Adres çubuğu gibi `example.com`bir `localhost:port#` şey gösterir.</span><span class="sxs-lookup"><span data-stu-id="f796e-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="f796e-264">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="f796e-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="f796e-265">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f796e-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="f796e-266">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="f796e-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="f796e-267">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f796e-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="f796e-268">İzlemeye izin vermek için **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f796e-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="f796e-269">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="f796e-269">This app doesn't track personal information.</span></span> <span data-ttu-id="f796e-270">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="f796e-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="f796e-272">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f796e-272">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="f796e-274">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f796e-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f796e-275">Sonraki</span><span class="sxs-lookup"><span data-stu-id="f796e-275">Next</span></span>](adding-controller.md)

::: moniker-end
