---
title: Core MVC ASP.NET ile başlayın
author: rick-anderson
description: Core MVC'ASP.NET nasıl başladığınızı öğrenin.
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662479"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="372c5-103">Core MVC ASP.NET ile başlayın</span><span class="sxs-lookup"><span data-stu-id="372c5-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="372c5-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="372c5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="372c5-105">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturmanın temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="372c5-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="372c5-106">Uygulama, film başlıklarının bir veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="372c5-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="372c5-107">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="372c5-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="372c5-108">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="372c5-108">Create a web app.</span></span>
> * <span data-ttu-id="372c5-109">Bir model ekleyin ve iskele.</span><span class="sxs-lookup"><span data-stu-id="372c5-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="372c5-110">Bir veritabanı ile çalışın.</span><span class="sxs-lookup"><span data-stu-id="372c5-110">Work with a database.</span></span>
> * <span data-ttu-id="372c5-111">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="372c5-111">Add search and validation.</span></span>

<span data-ttu-id="372c5-112">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="372c5-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="372c5-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="372c5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="372c5-117">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="372c5-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="372c5-119">Visual Studio'dan **yeni bir proje oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="372c5-120">**Core Web UygulamasıASP.NET** seçin ve ardından **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="372c5-122">Projemin adını **MvcMovie** olarak adlandırın ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="372c5-123">Projemin adını **MvcMovie** olarak adlandırmanız önemlidir, böylece kodu kopyaladiğinizde ad alanı eşleşir.</span><span class="sxs-lookup"><span data-stu-id="372c5-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/config.png)

* <span data-ttu-id="372c5-125">**Web Uygulaması(Model-View-Controller)** seçeneğini belirleyin ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="372c5-126">Yeni proje iletişim kutusu, .NET Core sol bölmede, ASP.NET Core web</span><span class="sxs-lookup"><span data-stu-id="372c5-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="372c5-127">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="372c5-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="372c5-128">Şu anda bir proje adı girerek ve birkaç seçenek seçerek çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="372c5-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="372c5-129">Bu temel bir başlangıç projesidir.</span><span class="sxs-lookup"><span data-stu-id="372c5-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="372c5-131">Öğretici VS Kodu ile familarity varsayar.</span><span class="sxs-lookup"><span data-stu-id="372c5-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="372c5-132">Daha fazla bilgi için VS Kodu ve [Visual Studio Code ile](#visual-studio-code-help) [başlarken](https://code.visualstudio.com/docs) yardım bakın.</span><span class="sxs-lookup"><span data-stu-id="372c5-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="372c5-133">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="372c5-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="372c5-134">Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="372c5-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="372c5-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="372c5-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="372c5-136">Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'MvcMovie'den eksiktir. Onları eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="372c5-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="372c5-137">**Evet'i** Seçin</span><span class="sxs-lookup"><span data-stu-id="372c5-137">Select **Yes**</span></span>

  * <span data-ttu-id="372c5-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* klasöründe yeni bir ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="372c5-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="372c5-139">`code -r MvcMovie`: Visual Studio Code'da *MvcMovie.csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="372c5-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-140">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="372c5-141">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-141">Select **File** > **New Solution**.</span></span>

  ![macOS Yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="372c5-143">**İleride** **.NET Core** > **App** > Web Uygulamasını **(Model-View-Controller) seçin.** ></span><span class="sxs-lookup"><span data-stu-id="372c5-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="372c5-145">Yeni **ASP.NET Core Web API iletişim günlüğünüze yapılandırıldığında** **,.NET Core 3.1'in** **Hedef Çerçevesini** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="372c5-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 seçimi](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="372c5-147">Projeyi **MvcMovie**olarak adlandırın ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="372c5-148">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="372c5-148">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="372c5-150">Uygulamayı hata ayıklama modunda çalıştırmak için **Ctrl-F5'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="372c5-151">Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="372c5-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="372c5-152">Adres çubuğunun gösterdiğine `localhost:port#` dikkat `example.com`edin, gibi bir şey değil.</span><span class="sxs-lookup"><span data-stu-id="372c5-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-153">Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="372c5-154">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="372c5-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="372c5-155">Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="372c5-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="372c5-156">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="372c5-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="372c5-157">Uygulamayı **Hata** Ayıklama menü öğesinden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="372c5-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="372c5-159">**IIS Express** düğmesini seçerek uygulamayı hata ayıklayabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="372c5-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Ekspresi](start-mvc/_static/iis_express.png)

  <span data-ttu-id="372c5-161">Aşağıdaki resim uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="372c5-161">The following image shows the app:</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="372c5-164">Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="372c5-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="372c5-165">Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `https://localhost:5001`başlattı ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="372c5-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="372c5-166">Adres çubuğu `localhost:port:5001` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="372c5-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-167">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="372c5-168">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="372c5-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="372c5-169">Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="372c5-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="372c5-170">Birçok geliştirici sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama modunu kullanmayı tercih ediyor.</span><span class="sxs-lookup"><span data-stu-id="372c5-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-172">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="372c5-173">Uygulamayı başlatmak için > **Hata Ayıklama olmadan** **Çalıştır'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="372c5-174">Mac için Visual Studio [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusubaşlatılır, bir `http://localhost:port`tarayıcı başlatır ve *bağlantı noktasının* rasgele seçilmiş bir bağlantı noktası numarası olduğu yere doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="372c5-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="372c5-175">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="372c5-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-176">Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="372c5-177">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="372c5-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="372c5-178">Uygulamayı çalıştırdığınızda farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="372c5-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="372c5-179">Uygulamayı **Çalıştır** menüsünden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="372c5-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="372c5-180">Aşağıdaki resim uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="372c5-180">The following image shows the app:</span></span>

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="372c5-182">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinin ve bazı kod yazmaya başlarsınız.</span><span class="sxs-lookup"><span data-stu-id="372c5-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="372c5-183">Sonraki</span><span class="sxs-lookup"><span data-stu-id="372c5-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="372c5-184">Bu öğretici, ASP.NET Core MVC web uygulaması oluşturmanın temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="372c5-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="372c5-185">Uygulama, film başlıklarının bir veritabanını yönetir.</span><span class="sxs-lookup"><span data-stu-id="372c5-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="372c5-186">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="372c5-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="372c5-187">Bir web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="372c5-187">Create a web app.</span></span>
> * <span data-ttu-id="372c5-188">Bir model ekleyin ve iskele.</span><span class="sxs-lookup"><span data-stu-id="372c5-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="372c5-189">Bir veritabanı ile çalışın.</span><span class="sxs-lookup"><span data-stu-id="372c5-189">Work with a database.</span></span>
> * <span data-ttu-id="372c5-190">Arama ve doğrulama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="372c5-190">Add search and validation.</span></span>

<span data-ttu-id="372c5-191">Sonunda, film verilerini yönetebilen ve görüntüleyebilen bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="372c5-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="372c5-192">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="372c5-192">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-195">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="372c5-196">Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="372c5-196">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="372c5-198">Visual Studio'dan **yeni bir proje oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="372c5-199">**Core Web UygulamasıASP.NET** seçin ve ardından **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="372c5-201">Projemin adını **MvcMovie** olarak adlandırın ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="372c5-202">Projemin adını **MvcMovie** olarak adlandırmanız önemlidir, böylece kodu kopyaladiğinizde ad alanı eşleşir.</span><span class="sxs-lookup"><span data-stu-id="372c5-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![yeni ASP.NET Çekirdek Web Uygulaması](start-mvc/_static/config.png)


* <span data-ttu-id="372c5-204">**Web Uygulaması(Model-View-Controller)** seçeneğini belirleyin ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="372c5-205">Yeni proje iletişim kutusu, .NET Core sol bölmede, ASP.NET Core web</span><span class="sxs-lookup"><span data-stu-id="372c5-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="372c5-206">Visual Studio, yeni oluşturduğunuz MVC projesi için varsayılan şablonu kullandı.</span><span class="sxs-lookup"><span data-stu-id="372c5-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="372c5-207">Şu anda bir proje adı girerek ve birkaç seçenek seçerek çalışan bir uygulamanız var.</span><span class="sxs-lookup"><span data-stu-id="372c5-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="372c5-208">Bu temel bir başlangıç projesi, ve başlamak için iyi bir yer.</span><span class="sxs-lookup"><span data-stu-id="372c5-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="372c5-210">Öğretici VS Kodu ile familarity varsayar.</span><span class="sxs-lookup"><span data-stu-id="372c5-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="372c5-211">Daha fazla bilgi için VS Kodu ve [Visual Studio Code ile](#visual-studio-code-help) [başlarken](https://code.visualstudio.com/docs) yardım bakın.</span><span class="sxs-lookup"><span data-stu-id="372c5-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="372c5-212">Entegre [terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açın.</span><span class="sxs-lookup"><span data-stu-id="372c5-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="372c5-213">Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="372c5-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="372c5-214">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="372c5-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="372c5-215">Oluşturmak için Gerekli varlıklarla birlikte bir iletişim kutusu görüntülenir **ve hata ayıklama 'MvcMovie'den eksiktir. Onları eklemek mi?**</span><span class="sxs-lookup"><span data-stu-id="372c5-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="372c5-216">**Evet'i** Seçin</span><span class="sxs-lookup"><span data-stu-id="372c5-216">Select **Yes**</span></span>

  * <span data-ttu-id="372c5-217">`dotnet new mvc -o MvcMovie`: *MvcMovie* klasöründe yeni bir ASP.NET Core MVC projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="372c5-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="372c5-218">`code -r MvcMovie`: Visual Studio Code'da *MvcMovie.csproj* proje dosyasını yükler.</span><span class="sxs-lookup"><span data-stu-id="372c5-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-219">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="372c5-220">**Dosya** > **Yeni Çözüm**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-220">Select **File** > **New Solution**.</span></span>

  ![macOS Yeni çözüm](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="372c5-222">**İleride** **.NET Core** > **App** > Web Uygulamasını **(Model-View-Controller) seçin.** ></span><span class="sxs-lookup"><span data-stu-id="372c5-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Yeni proje iletişim kutusu](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="372c5-224">Yeni **ASP.NET Core Web API iletişim günlüğünüzü yapılandırın,** **.NET Core 2.2'nin**varsayılan **Hedef Çerçevesini** kabul edin.</span><span class="sxs-lookup"><span data-stu-id="372c5-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 seçimi](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="372c5-226">Projeyi **MvcMovie**olarak adlandırın ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="372c5-227">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="372c5-227">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="372c5-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="372c5-229">Uygulamayı hata ayıklama modunda çalıştırmak için **Ctrl-F5'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="372c5-230">Visual Studio [IIS Express'i](/iis/extensions/introduction-to-iis-express/iis-express-overview) başlatır ve uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="372c5-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="372c5-231">Adres çubuğunun gösterdiğine `localhost:port#` dikkat `example.com`edin, gibi bir şey değil.</span><span class="sxs-lookup"><span data-stu-id="372c5-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-232">Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="372c5-233">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="372c5-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="372c5-234">Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="372c5-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="372c5-235">Birçok geliştirici uygulamayı hemen başlatmak ve değişiklikleri görüntülemek için hata ayıklamasız modu kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="372c5-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="372c5-236">Uygulamayı **Hata** Ayıklama menü öğesinden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="372c5-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![Hata ayıklama menüsü](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="372c5-238">**IIS Express** düğmesini seçerek uygulamayı hata ayıklayabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="372c5-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Ekspresi](start-mvc/_static/iis_express.png)

* <span data-ttu-id="372c5-240">İzlemeye onay vermek için **Kabul et'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="372c5-241">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="372c5-241">This app doesn't track personal information.</span></span> <span data-ttu-id="372c5-242">Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="372c5-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="372c5-244">Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="372c5-244">The following image shows the app after accepting tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="372c5-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="372c5-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="372c5-247">Hata ayıklama olmadan çalıştırmak için Ctrl+F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="372c5-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="372c5-248">Görsel Studio Code [Kerkenez](xref:fundamentals/servers/kestrel)başlar, bir tarayıcı `https://localhost:5001`başlattı ve gezinir .</span><span class="sxs-lookup"><span data-stu-id="372c5-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="372c5-249">Adres çubuğu `localhost:port:5001` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="372c5-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-250">Bunun nedeni, `localhost` yerel bilgisayarın standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="372c5-251">Localhost yalnızca yerel bilgisayardan web isteklerine hizmet eder.</span><span class="sxs-lookup"><span data-stu-id="372c5-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="372c5-252">Uygulamayı Ctrl+F5 (hata ayıklama modu olmayan mod) ile başlatmak, kod değişiklikleri yapmanızı, dosyayı kaydetmenizi, tarayıcıyı yenilemenizi ve kod değişikliklerini görmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="372c5-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="372c5-253">Birçok geliştirici sayfayı yenilemek ve değişiklikleri görüntülemek için hata ayıklama modunu kullanmayı tercih ediyor.</span><span class="sxs-lookup"><span data-stu-id="372c5-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="372c5-254">İzlemeye onay vermek için **Kabul et'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="372c5-255">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="372c5-255">This app doesn't track personal information.</span></span> <span data-ttu-id="372c5-256">Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="372c5-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/privacy.png)

  <span data-ttu-id="372c5-258">Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="372c5-258">The following image shows the app after accepting tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="372c5-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="372c5-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="372c5-261">Uygulamayı başlatmak için > **Hata Ayıklama olmadan** **Çalıştır'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="372c5-262">Mac için Visual Studio [Kestrel](xref:fundamentals/servers/index#kestrel) sunucusubaşlatılır, bir `http://localhost:port`tarayıcı başlatır ve *bağlantı noktasının* rasgele seçilmiş bir bağlantı noktası numarası olduğu yere doğru yol alar.</span><span class="sxs-lookup"><span data-stu-id="372c5-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="372c5-263">Adres çubuğu `localhost:port#` gösterir ve `example.com`gibi bir şey .</span><span class="sxs-lookup"><span data-stu-id="372c5-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="372c5-264">Bunun nedeni, `localhost` yerel bilgisayarınız için standart ana bilgisayar adı olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="372c5-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="372c5-265">Visual Studio bir web projesi oluşturduğunda, web sunucusu için rastgele bir bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="372c5-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="372c5-266">Uygulamayı çalıştırdığınızda farklı bir bağlantı noktası numarası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="372c5-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="372c5-267">Uygulamayı **Çalıştır** menüsünden hata ayıklama veya hata ayıklama modunda başlatabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="372c5-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="372c5-268">İzlemeye onay vermek için **Kabul et'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="372c5-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="372c5-269">Bu uygulama kişisel bilgileri izlemez.</span><span class="sxs-lookup"><span data-stu-id="372c5-269">This app doesn't track personal information.</span></span> <span data-ttu-id="372c5-270">Oluşturulan şablon, Genel Veri [Koruma Yönetmeliği'ni (GDPR)](xref:security/gdpr)karşılamaya yardımcı olacak varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="372c5-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="372c5-272">Aşağıdaki resim, izlemeyi kabul ettikten sonra uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="372c5-272">The following image shows the app after accepting tracking:</span></span>

  ![Ana Sayfa veya Dizin sayfası](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="372c5-274">Bu öğreticinin bir sonraki bölümünde, MVC hakkında bilgi edinin ve bazı kod yazmaya başlarsınız.</span><span class="sxs-lookup"><span data-stu-id="372c5-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="372c5-275">Sonraki</span><span class="sxs-lookup"><span data-stu-id="372c5-275">Next</span></span>](adding-controller.md)

::: moniker-end
