---
title: ASP.NET Core MVC ile çalışmaya başlama
author: rick-anderson
description: ASP.NET Core MVC ile çalışmaya başlama hakkında bilgi edinin.
ms.author: riande
ms.date: 10/16/2019
no-loc:
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
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629996"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="108a9-103">ASP.NET Core MVC ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="108a9-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="108a9-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="108a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="108a9-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="108a9-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="108a9-106">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="108a9-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="108a9-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="108a9-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="108a9-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="108a9-108">Create a web app.</span></span>
> * <span data-ttu-id="108a9-109">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="108a9-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="108a9-110">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="108a9-110">Work with a database.</span></span>
> * <span data-ttu-id="108a9-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="108a9-111">Add search and validation.</span></span>

<span data-ttu-id="108a9-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="108a9-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="108a9-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="108a9-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="108a9-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="108a9-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="108a9-119">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="108a9-120">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="108a9-122">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="108a9-123">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="108a9-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="108a9-125">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="108a9-126">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="108a9-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="108a9-127">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="108a9-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="108a9-128">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="108a9-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="108a9-129">Bu, temel bir başlatıcı projem.</span><span class="sxs-lookup"><span data-stu-id="108a9-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="108a9-131">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="108a9-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="108a9-132">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="108a9-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="108a9-133">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="108a9-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="108a9-134">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="108a9-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="108a9-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="108a9-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="108a9-136">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="108a9-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="108a9-137">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="108a9-137">Select **Yes**</span></span>

  * <span data-ttu-id="108a9-138">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="108a9-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="108a9-139">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="108a9-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="108a9-141">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-141">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="108a9-143">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="108a9-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="108a9-144">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web uygulaması şablon seçimi](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="108a9-146">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="108a9-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="108a9-147">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="108a9-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="108a9-148">**Hedef çerçeve**seçme seçeneği sunulursa, en son 3. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="108a9-149">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-149">Select **Next**.</span></span>

* <span data-ttu-id="108a9-150">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS projeyi Adlandır](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="108a9-152">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="108a9-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="108a9-154">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="108a9-155">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="108a9-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="108a9-156">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-157">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="108a9-158">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="108a9-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="108a9-159">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="108a9-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="108a9-160">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="108a9-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="108a9-161">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="108a9-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="108a9-163">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="108a9-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="108a9-165">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="108a9-165">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="108a9-168">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="108a9-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="108a9-169">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="108a9-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="108a9-170">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-171">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="108a9-172">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="108a9-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="108a9-173">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="108a9-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="108a9-174">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="108a9-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-176">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="108a9-177">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="108a9-178">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="108a9-179">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-180">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="108a9-181">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="108a9-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="108a9-182">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="108a9-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="108a9-183">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="108a9-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="108a9-184">Aşağıdaki görüntüde uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="108a9-184">The following image shows the app:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="108a9-186">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="108a9-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="108a9-187">Sonraki</span><span class="sxs-lookup"><span data-stu-id="108a9-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="108a9-188">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturma hakkında temel bilgileri öğretir.</span><span class="sxs-lookup"><span data-stu-id="108a9-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="108a9-189">Uygulama, bir film başlıkları veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="108a9-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="108a9-190">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="108a9-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="108a9-191">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="108a9-191">Create a web app.</span></span>
> * <span data-ttu-id="108a9-192">Bir modeli ekleyin ve yapı iskelesi yapın.</span><span class="sxs-lookup"><span data-stu-id="108a9-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="108a9-193">Bir veritabanıyla çalışın.</span><span class="sxs-lookup"><span data-stu-id="108a9-193">Work with a database.</span></span>
> * <span data-ttu-id="108a9-194">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="108a9-194">Add search and validation.</span></span>

<span data-ttu-id="108a9-195">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız vardır.</span><span class="sxs-lookup"><span data-stu-id="108a9-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="108a9-196">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="108a9-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="108a9-200">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="108a9-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="108a9-202">Visual Studio 'da **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="108a9-203">**ASP.NET Core Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="108a9-205">Projeyi **Mvcfilmi** olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="108a9-206">Kodu kopyaladığınızda, ad alanının eşleşmesi için, projeyi **Mvcfilmi** olarak adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="108a9-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![Yeni ASP.NET Core Web uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="108a9-208">**Web uygulaması (Model-View-Controller)** öğesini seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="108a9-209">Yeni proje iletişim kutusu, sol bölmede .NET Core, ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="108a9-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="108a9-210">Visual Studio, az önce oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="108a9-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="108a9-211">Şimdi bir proje adı girip birkaç seçenek belirleyerek, çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="108a9-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="108a9-212">Bu, temel bir başlatıcı projem ve başlamak için iyi bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="108a9-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="108a9-214">Öğreticide familar, VS Code ile varsayılmıştır.</span><span class="sxs-lookup"><span data-stu-id="108a9-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="108a9-215">Daha fazla bilgi için bkz. [vs Code kullanmaya](https://code.visualstudio.com/docs) başlama ve [Yardım Visual Studio Code](#visual-studio-code-help) .</span><span class="sxs-lookup"><span data-stu-id="108a9-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="108a9-216">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="108a9-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="108a9-217">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="108a9-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="108a9-218">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="108a9-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="108a9-219">**Gerekli varlıkların derlenmesi ve hata ayıklaması için ' Mvcfilmi ' içinde eksik olan bir iletişim kutusu görüntülenir. Bunları ekleyin mi?**</span><span class="sxs-lookup"><span data-stu-id="108a9-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="108a9-220">**Evet** ' i seçin</span><span class="sxs-lookup"><span data-stu-id="108a9-220">Select **Yes**</span></span>

  * <span data-ttu-id="108a9-221">`dotnet new mvc -o MvcMovie`: *Mvcmovie* klasöründe yeni BIR ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="108a9-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="108a9-222">`code -r MvcMovie`: Visual Studio Code ' de *Mvcmovie. csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="108a9-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-223">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="108a9-224">**Dosya** > **yeni çözüm**' ü seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-224">Select **File** > **New Solution**.</span></span>

  ![macOS yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="108a9-226">Sürüm 8,6 ' den önceki Mac için Visual Studio, ardından **.NET Core**  >  **uygulama**  >  **Web uygulaması (Model-View-Controller)**' ı seçin  >  **Next**.</span><span class="sxs-lookup"><span data-stu-id="108a9-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="108a9-227">Sürüm 8,6 veya sonraki sürümlerde **Web ve konsol**  >  **uygulaması**  >  **Web uygulaması (Model-View-Controller)**  >  **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="108a9-228">**Yeni Web uygulamanızı yapılandırın** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="108a9-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="108a9-229">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="108a9-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="108a9-230">**Hedef çerçeve**seçme seçeneği sunulursa, en son 2. x sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="108a9-231">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-231">Select **Next**.</span></span>

* <span data-ttu-id="108a9-232">Projeyi **Mvcfilmi**olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="108a9-233">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="108a9-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="108a9-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="108a9-235">Uygulamayı hata ayıklamasız modda çalıştırmak için **CTRL-F5** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="108a9-236">Visual Studio [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlar ve uygulamayı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="108a9-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="108a9-237">Adres çubuğunda, `localhost:port#` gibi bir şey gösterilmediğine dikkat edin `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-238">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="108a9-239">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="108a9-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="108a9-240">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="108a9-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="108a9-241">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="108a9-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="108a9-242">**Hata ayıklama menü öğesinden** uygulamayı hata ayıklama veya hata ayıklama olmayan modda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="108a9-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="108a9-244">**IIS Express** düğmesini seçerek uygulamada hata ayıklaması yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="108a9-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="108a9-246">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="108a9-247">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="108a9-247">This app doesn't track personal information.</span></span> <span data-ttu-id="108a9-248">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="108a9-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="108a9-250">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="108a9-250">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="108a9-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="108a9-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="108a9-253">Hata ayıklayıcı olmadan çalıştırmak için CTRL + F5 tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="108a9-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="108a9-254">Visual Studio Code, [Kestrel](xref:fundamentals/servers/kestrel)başlatır, bir tarayıcı başlatır ve şuraya gider `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="108a9-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="108a9-255">Adres çubuğu `localhost:port:5001` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-256">Bunun nedeni `localhost` , yerel bilgisayar için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="108a9-257">Localhost yalnızca yerel bilgisayardan Web isteklerine hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="108a9-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="108a9-258">Uygulamayı CTRL + F5 (hata ayıklama modu) ile başlatmak, kod değişiklikleri yapmanıza, dosyayı kaydetmenize, tarayıcıyı yenilemanıza ve kod değişikliklerini görmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="108a9-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="108a9-259">Birçok geliştirici, sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama olmayan modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="108a9-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="108a9-260">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="108a9-261">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="108a9-261">This app doesn't track personal information.</span></span> <span data-ttu-id="108a9-262">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="108a9-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="108a9-264">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="108a9-264">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="108a9-266">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="108a9-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="108a9-267">**Run**  >  Uygulamayı başlatmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="108a9-268">Mac için Visual Studio, [Kestrel](xref:fundamentals/servers/index#kestrel) Server 'ı başlatır, bir tarayıcı başlatır ve ' a gider ve `http://localhost:port` *bağlantı noktası* rastgele seçilmiş bir bağlantı noktası numarasıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="108a9-269">Adres çubuğu `localhost:port#` gibi bir şey gösterir `example.com` .</span><span class="sxs-lookup"><span data-stu-id="108a9-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="108a9-270">Bunun nedeni `localhost` , Yerel bilgisayarınız için Standart ana bilgisayar adıdır.</span><span class="sxs-lookup"><span data-stu-id="108a9-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="108a9-271">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="108a9-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="108a9-272">Uygulamayı çalıştırdığınızda, farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="108a9-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="108a9-273">Uygulamayı **Çalıştır** menüsünden Hata Ayıkla veya hata ayıklama olmayan modda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="108a9-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="108a9-274">İzlemeye onay vermek için **Kabul Et**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="108a9-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="108a9-275">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="108a9-275">This app doesn't track personal information.</span></span> <span data-ttu-id="108a9-276">Şablon tarafından oluşturulan kod, [genel veri koruma yönetmeliği (GDPR)](xref:security/gdpr)buluşmanıza yardımcı olan varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="108a9-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="108a9-278">Aşağıdaki görüntüde izlemeyi kabul ettikten sonra uygulama gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="108a9-278">The following image shows the app after accepting tracking:</span></span>

  ![Giriş veya dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="108a9-280">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinirsiniz ve kod yazmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="108a9-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="108a9-281">Sonraki</span><span class="sxs-lookup"><span data-stu-id="108a9-281">Next</span></span>](adding-controller.md)

::: moniker-end
