---
title: ASP.NET Core kullanmaya başlayınSignalR
author: bradygaster
description: Bu öğreticide, ASP.NET Core kullanan bir sohbet uygulaması oluşturacaksınız SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 51b9eae0d4746001696e0795467eaf4c0ab2c990
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022036"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="92c1d-103">Öğretici: ASP.NET Core kullanmaya başlayınSignalR</span><span class="sxs-lookup"><span data-stu-id="92c1d-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92c1d-104">Bu öğreticide, kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="92c1d-105">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="92c1d-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="92c1d-106">Bir Web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-106">Create a web project.</span></span>
> * <span data-ttu-id="92c1d-107">SignalRİstemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="92c1d-108">Bir SignalR hub oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="92c1d-109">Projeyi kullanmak üzere yapılandırın SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="92c1d-110">Herhangi bir istemciden tüm bağlı istemcilere ileti gönderen kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="92c1d-111">Sonunda, çalışan bir sohbet uygulamanız olacaktır:</span><span class="sxs-lookup"><span data-stu-id="92c1d-111">At the end, you'll have a working chat app:</span></span>

![::: No-Loc (SignalR)::: örnek uygulama](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="92c1d-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="92c1d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="92c1d-117">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="92c1d-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="92c1d-119">Menüden **dosya > yeni proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="92c1d-120">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması**' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="92c1d-121">**Yeni projenizi yapılandırın** iletişim kutusunda, proje \* SignalR sohbetini\*adlandırın ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="92c1d-122">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **.net Core** ve **ASP.NET Core 3,0**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="92c1d-123">Sayfaların kullanıldığı bir proje oluşturmak için **Web uygulaması** Razor ' nı seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="92c1d-126">[Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) ' i yeni proje klasörünün oluşturulacağı klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="92c1d-127">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="92c1d-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92c1d-129">Menüden **dosya > yeni çözüm**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="92c1d-130">**.NET Core > App > Web uygulaması** ' nı ( **Web uygulaması (Model-View-Controller)** seçmeyin) seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="92c1d-131">**Hedef çerçevenin** **.NET Core 3,0**olarak ayarlandığından emin olun ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="92c1d-132">Projenin \* SignalR sohbetini\*adlandırın ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="92c1d-133">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="92c1d-133">Add the SignalR client library</span></span>

<span data-ttu-id="92c1d-134">SignalRSunucu kitaplığı ASP.NET Core 3,0 paylaşılan çerçevesine dahildir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="92c1d-135">JavaScript istemci kitaplığı projeye otomatik olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="92c1d-136">Bu öğreticide, istemci kitaplığını *unpkg*'den almak Için kitaplık Yöneticisi 'Ni (Libman) kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="92c1d-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="92c1d-137">unpkg, Node.js Paket Yöneticisi NPM 'de bulunan her şeyi teslim edebilen bir içerik teslim ağı (CDN).</span><span class="sxs-lookup"><span data-stu-id="92c1d-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="92c1d-139">**Çözüm Gezgini**' de projeye sağ tıklayın ve **Add** > **istemci tarafı kitaplığı**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="92c1d-140">**Istemci tarafı kitaplığı Ekle** Iletişim kutusunda **sağlayıcı** için **unpkg**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="92c1d-141">**Kitaplık**için girin `@microsoft/signalr@latest` .</span><span class="sxs-lookup"><span data-stu-id="92c1d-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="92c1d-142">**Belirli dosyaları seç**' i seçin, *dağ/Browser* klasörünü genişletin ve *signalr.js* ve *signalr.min.js*' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="92c1d-143">**Hedef konumu** *Wwwroot/js/SignalR/* olarak ayarlayın ve **yüklemeyi**seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-kitaplık Seç](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="92c1d-145">LibMan, bir *Wwwroot/js/SignalR* klasörü oluşturur ve seçilen dosyaları buna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="92c1d-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="92c1d-147">Tümleşik terminalde, LibMan 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="92c1d-148">SignalRLibMan kullanarak istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="92c1d-149">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="92c1d-150">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="92c1d-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="92c1d-151">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="92c1d-152">Dosyaları *Wwwroot/js/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="92c1d-153">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="92c1d-153">Copy only the specified files.</span></span>

  <span data-ttu-id="92c1d-154">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="92c1d-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-155">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92c1d-156">**Terminalde**, Libman 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="92c1d-157">Proje klasörüne gidin ( \* SignalR sohbet. csproj\* dosyasını içeren bir dosya).</span><span class="sxs-lookup"><span data-stu-id="92c1d-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="92c1d-158">SignalRLibMan kullanarak istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="92c1d-159">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="92c1d-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="92c1d-160">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="92c1d-161">Dosyaları *Wwwroot/js/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="92c1d-162">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="92c1d-162">Copy only the specified files.</span></span>

  <span data-ttu-id="92c1d-163">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="92c1d-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="92c1d-164">Merkez oluşturma SignalR</span><span class="sxs-lookup"><span data-stu-id="92c1d-164">Create a SignalR hub</span></span>

<span data-ttu-id="92c1d-165">*Hub* , istemci-sunucu iletişimini işleyen yüksek düzeyli bir işlem hattı görevi gören bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="92c1d-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="92c1d-166">SignalRSohbet projesi klasöründe bir *hub* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="92c1d-167">*Hub 'lar* klasöründe, aşağıdaki kodla bir *ChatHub.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="92c1d-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="92c1d-168">`ChatHub`Sınıf sınıfından devralır SignalR `Hub` .</span><span class="sxs-lookup"><span data-stu-id="92c1d-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="92c1d-169">`Hub`Sınıfı bağlantıları, grupları ve mesajlaşmayı yönetir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="92c1d-170">`SendMessage`Yöntemi, tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="92c1d-171">Yöntemi çağıran JavaScript istemci kodu Öğreticinin ilerleyen kısımlarında gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="92c1d-172">SignalRmaksimum ölçeklenebilirlik sağlamak için kod zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="92c1d-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="92c1d-173">YapılandırmaSignalR</span><span class="sxs-lookup"><span data-stu-id="92c1d-173">Configure SignalR</span></span>

<span data-ttu-id="92c1d-174">SignalRSunucunun istekleri geçirilecek şekilde yapılandırılması gerekir SignalR SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="92c1d-175">Aşağıdaki Vurgulanan kodu *Startup.cs* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="92c1d-176">Bu değişiklikler SignalR ASP.NET Core bağımlılığı ekleme ve yönlendirme sistemlerine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="92c1d-177">SignalRİstemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="92c1d-177">Add SignalR client code</span></span>

* <span data-ttu-id="92c1d-178">*Pages\ındex.cshtml* içindeki içeriği şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="92c1d-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="92c1d-179">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="92c1d-179">The preceding code:</span></span>

  * <span data-ttu-id="92c1d-180">Ad ve ileti metni ve Gönder düğmesi için metin kutuları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="92c1d-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="92c1d-181">`id="messagesList"`Hub 'dan alınan iletileri görüntülemek için içeren bir liste oluşturur SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="92c1d-182">, SignalR Bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna ve betik başvurularını içerir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="92c1d-183">*Wwwroot/js* klasöründe, aşağıdaki kodla bir *chat.js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="92c1d-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="92c1d-184">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="92c1d-184">The preceding code:</span></span>

  * <span data-ttu-id="92c1d-185">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="92c1d-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="92c1d-186">, Hub 'a ileti gönderen bir işleyiciye Gönder düğmesine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="92c1d-187">, Hub 'dan iletileri alan ve bunları listeye ekleyen bir işleyici olan bağlantı nesnesine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="92c1d-188">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="92c1d-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92c1d-190">Uygulamayı hata ayıklamadan çalıştırmak için **CTRL + F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="92c1d-192">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="92c1d-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-193">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92c1d-194">Menüden, **hata ayıklama olmadan başlat > Çalıştır**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="92c1d-195">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="92c1d-196">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Ileti gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="92c1d-197">Ad ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-197">The name and message are displayed on both pages instantly.</span></span>

  ![::: No-Loc (SignalR)::: örnek uygulama](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="92c1d-199">Uygulama işe yaramazsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="92c1d-200">HTML ve JavaScript kodunuzla ilgili hatalarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="92c1d-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="92c1d-201">Örneğin, *signalr.js* yönlendirenden farklı bir klasöre yerleştirdiğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="92c1d-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="92c1d-202">Bu durumda, bu dosyaya başvuru çalışmaz ve konsolunda 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="92c1d-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="92c1d-203">![signalr.js bulunamadı hatası](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="92c1d-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="92c1d-204">Chrome 'da hata ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY alırsanız, geliştirme sertifikanızı güncelleştirmek için şu komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="92c1d-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="92c1d-205">Bu öğreticide, kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="92c1d-206">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="92c1d-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="92c1d-207">Bir Web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-207">Create a web project.</span></span>   
> * <span data-ttu-id="92c1d-208">SignalRİstemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="92c1d-209">Bir SignalR hub oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="92c1d-210">Projeyi kullanmak üzere yapılandırın SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="92c1d-211">Herhangi bir istemciden tüm bağlı istemcilere ileti gönderen kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="92c1d-212">Sonunda, çalışan bir sohbet uygulamasına sahipsiniz::: ![ : No-Loc (SignalR)::: örnek uygulama](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="92c1d-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="92c1d-213">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="92c1d-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-216">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="92c1d-217">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="92c1d-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="92c1d-219">Menüden **dosya > yeni proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="92c1d-220">**Yeni proje** iletişim kutusunda, **Visual C# > Web > ASP.NET Core Web uygulaması > yüklü**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="92c1d-221">Projenin \* SignalR sohbetini\*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-221">Name the project *SignalRChat*.</span></span>   

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="92c1d-223">Sayfaları kullanan bir proje oluşturmak için **Web uygulaması** ' nı seçin Razor .</span><span class="sxs-lookup"><span data-stu-id="92c1d-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="92c1d-224">**.NET Core**'un hedef çerçevesini seçin, **ASP.NET Core 2,2**' i seçin ve **Tamam**' ı tıklatın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="92c1d-227">[Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) ' i yeni proje klasörünün oluşturulacağı klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="92c1d-228">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="92c1d-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="92c1d-230">Menüden **dosya > yeni çözüm**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="92c1d-231">**.NET Core > App > ASP.NET Core Web uygulaması** ' nı ( **ASP.NET Core Web uygulaması (MVC)** seçmeyin) seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="92c1d-232">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-232">Select **Next**.</span></span>  

* <span data-ttu-id="92c1d-233">Projenin \* SignalR sohbetini\*adlandırın ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="92c1d-234">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="92c1d-234">Add the SignalR client library</span></span> 

<span data-ttu-id="92c1d-235">SignalRSunucu kitaplığı, metapackage 'e dahildir `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="92c1d-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="92c1d-236">JavaScript istemci kitaplığı projeye otomatik olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="92c1d-237">Bu öğreticide, istemci kitaplığını *unpkg*'den almak Için kitaplık Yöneticisi 'Ni (Libman) kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="92c1d-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="92c1d-238">unpkg, Node.js Paket Yöneticisi NPM 'de bulunan her şeyi teslim edebilen bir içerik teslim ağı (CDN).</span><span class="sxs-lookup"><span data-stu-id="92c1d-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="92c1d-240">**Çözüm Gezgini**' de projeye sağ tıklayın ve **Add** > **istemci tarafı kitaplığı**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="92c1d-241">**Istemci tarafı kitaplığı Ekle** Iletişim kutusunda **sağlayıcı** için **unpkg**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="92c1d-242">**Kitaplık**için `@microsoft/signalr@3` , öğesini girin ve Önizleme olmayan en son sürümü seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-kitaplık Seç](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="92c1d-244">**Belirli dosyaları seç**' i seçin, *dağ/Browser* klasörünü genişletin ve *signalr.js* ve *signalr.min.js*' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="92c1d-245">**Hedef konumu** *Wwwroot/lib/SignalR/* olarak ayarlayın ve **yüklemeyi**seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-dosya ve hedef seçme](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="92c1d-247">LibMan, bir *Wwwroot/LIB/SignalR* klasörü oluşturur ve seçilen dosyaları buna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="92c1d-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="92c1d-249">Tümleşik terminalde, LibMan 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="92c1d-250">SignalRLibMan kullanarak istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="92c1d-251">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="92c1d-252">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="92c1d-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="92c1d-253">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="92c1d-254">Dosyaları *Wwwroot/lib/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="92c1d-255">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="92c1d-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="92c1d-256">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="92c1d-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-257">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="92c1d-258">**Terminalde**, Libman 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="92c1d-259">Proje klasörüne gidin ( \* SignalR sohbet. csproj\* dosyasını içeren bir dosya).</span><span class="sxs-lookup"><span data-stu-id="92c1d-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="92c1d-260">SignalRLibMan kullanarak istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="92c1d-261">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="92c1d-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="92c1d-262">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="92c1d-263">Dosyaları *Wwwroot/lib/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="92c1d-264">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="92c1d-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="92c1d-265">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="92c1d-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="92c1d-266">Merkez oluşturma SignalR</span><span class="sxs-lookup"><span data-stu-id="92c1d-266">Create a SignalR hub</span></span>   

<span data-ttu-id="92c1d-267">*Hub* , istemci-sunucu iletişimini işleyen yüksek düzeyli bir işlem hattı görevi gören bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="92c1d-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="92c1d-268">SignalRSohbet projesi klasöründe bir *hub* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="92c1d-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="92c1d-269">*Hub 'lar* klasöründe, aşağıdaki kodla bir *ChatHub.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="92c1d-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="92c1d-270">`ChatHub`Sınıf sınıfından devralır SignalR `Hub` .</span><span class="sxs-lookup"><span data-stu-id="92c1d-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="92c1d-271">`Hub`Sınıfı bağlantıları, grupları ve mesajlaşmayı yönetir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="92c1d-272">`SendMessage`Yöntemi, tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="92c1d-273">Yöntemi çağıran JavaScript istemci kodu Öğreticinin ilerleyen kısımlarında gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="92c1d-274">SignalRmaksimum ölçeklenebilirlik sağlamak için kod zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="92c1d-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="92c1d-275">YapılandırmaSignalR</span><span class="sxs-lookup"><span data-stu-id="92c1d-275">Configure SignalR</span></span>  

<span data-ttu-id="92c1d-276">SignalRSunucunun istekleri geçirilecek şekilde yapılandırılması gerekir SignalR SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="92c1d-277">Aşağıdaki Vurgulanan kodu *Startup.cs* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="92c1d-278">Bu değişiklikler SignalR ASP.NET Core bağımlılık ekleme sistemine ve ara yazılım ardışık düzenine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="92c1d-279">SignalRİstemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="92c1d-279">Add SignalR client code</span></span>    

* <span data-ttu-id="92c1d-280">*Pages\ındex.cshtml* içindeki içeriği şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="92c1d-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="92c1d-281">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="92c1d-281">The preceding code:</span></span>   

  * <span data-ttu-id="92c1d-282">Ad ve ileti metni ve Gönder düğmesi için metin kutuları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="92c1d-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="92c1d-283">`id="messagesList"`Hub 'dan alınan iletileri görüntülemek için içeren bir liste oluşturur SignalR .</span><span class="sxs-lookup"><span data-stu-id="92c1d-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="92c1d-284">, SignalR Bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna ve betik başvurularını içerir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="92c1d-285">*Wwwroot/js* klasöründe, aşağıdaki kodla bir *chat.js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="92c1d-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="92c1d-286">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="92c1d-286">The preceding code:</span></span>   

  * <span data-ttu-id="92c1d-287">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="92c1d-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="92c1d-288">, Hub 'a ileti gönderen bir işleyiciye Gönder düğmesine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="92c1d-289">, Hub 'dan iletileri alan ve bunları listeye ekleyen bir işleyici olan bağlantı nesnesine ekler.</span><span class="sxs-lookup"><span data-stu-id="92c1d-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="92c1d-290">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="92c1d-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="92c1d-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="92c1d-292">Uygulamayı hata ayıklamadan çalıştırmak için **CTRL + F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="92c1d-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92c1d-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="92c1d-294">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="92c1d-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="92c1d-295">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92c1d-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92c1d-296">Menüden, **hata ayıklama olmadan başlat > Çalıştır**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="92c1d-297">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="92c1d-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="92c1d-298">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Ileti gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="92c1d-299">Ad ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="92c1d-299">The name and message are displayed on both pages instantly.</span></span>   

  ![::: No-Loc (SignalR)::: örnek uygulama](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="92c1d-301">Uygulama işe yaramazsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="92c1d-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="92c1d-302">HTML ve JavaScript kodunuzla ilgili hatalarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="92c1d-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="92c1d-303">Örneğin, *signalr.js* yönlendirenden farklı bir klasöre yerleştirdiğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="92c1d-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="92c1d-304">Bu durumda, bu dosyaya başvuru çalışmaz ve konsolunda 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="92c1d-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="92c1d-305">![signalr.js bulunamadı hatası](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="92c1d-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="92c1d-306">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="92c1d-306">Additional resources</span></span> 
* [<span data-ttu-id="92c1d-307">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="92c1d-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
