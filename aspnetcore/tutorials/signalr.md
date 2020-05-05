---
title: ASP.NET Core kullanmaya başlayınSignalR
author: bradygaster
description: Bu öğreticide, ASP.NET Core SignalRkullanan bir sohbet uygulaması oluşturacaksınız.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 3fab97781fe354fd3d244880a00353957d7cfabf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774567"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="fcfba-103">Öğretici: ASP.NET Core SignalR ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="fcfba-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fcfba-104">Bu öğreticide, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="fcfba-105">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="fcfba-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fcfba-106">Bir Web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-106">Create a web project.</span></span>
> * <span data-ttu-id="fcfba-107">SignalR istemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="fcfba-108">Bir SignalR hub 'ı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="fcfba-109">Projeyi SignalR kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="fcfba-110">Herhangi bir istemciden tüm bağlı istemcilere ileti gönderen kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="fcfba-111">Sonunda, çalışan bir sohbet uygulamanız olacaktır:</span><span class="sxs-lookup"><span data-stu-id="fcfba-111">At the end, you'll have a working chat app:</span></span>

![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="fcfba-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="fcfba-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="fcfba-117">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="fcfba-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="fcfba-119">Menüden **dosya > yeni proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="fcfba-120">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması**' nı seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="fcfba-121">**Yeni projenizi yapılandırın** iletişim kutusunda, proje *signalrchat*adını adlandırın ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="fcfba-122">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **.net Core** ve **ASP.NET Core 3,0**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="fcfba-123">Razor Pages kullanan bir proje oluşturmak için **Web uygulaması** ' nı seçin ve ardından **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="fcfba-126">[Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) ' i yeni proje klasörünün oluşturulacağı klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="fcfba-127">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fcfba-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fcfba-129">Menüden **dosya > yeni çözüm**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="fcfba-130">**.NET Core > App > Web uygulaması** ' nı ( **Web uygulaması (Model-View-Controller)** seçmeyin) seçin ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="fcfba-131">**Hedef çerçevenin** **.NET Core 3,0**olarak ayarlandığından emin olun ve ardından **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="fcfba-132">Projeyi *Signalrchat*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="fcfba-133">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="fcfba-133">Add the SignalR client library</span></span>

<span data-ttu-id="fcfba-134">SignalR sunucu kitaplığı ASP.NET Core 3,0 paylaşılan çerçevesine dahildir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="fcfba-135">JavaScript istemci kitaplığı projeye otomatik olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="fcfba-136">Bu öğreticide, istemci kitaplığını *unpkg*'den almak Için kitaplık Yöneticisi 'Ni (Libman) kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="fcfba-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="fcfba-137">unpkg, Node. js Paket Yöneticisi NPM 'de bulunan her şeyi teslim edebilen bir içerik teslim ağı (CDN).</span><span class="sxs-lookup"><span data-stu-id="fcfba-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="fcfba-139">**Çözüm Gezgini**' de projeye sağ tıklayın ve **istemci tarafı kitaplığı** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="fcfba-140">**Istemci tarafı kitaplığı Ekle** Iletişim kutusunda **sağlayıcı** için **unpkg**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="fcfba-141">**Kitaplık**için girin `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="fcfba-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="fcfba-142">**Belirli dosyaları seç**' i seçin, *dağ/Browser* klasörünü genişletin ve *SignalR. js* ve *SignalR. min. js*' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="fcfba-143">**Hedef konumu** *Wwwroot/js/SignalR/* olarak ayarlayın ve **yüklemeyi**seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-kitaplık Seç](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="fcfba-145">LibMan, bir *Wwwroot/js/SignalR* klasörü oluşturur ve seçilen dosyaları buna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="fcfba-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="fcfba-147">Tümleşik terminalde, LibMan 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="fcfba-148">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="fcfba-149">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="fcfba-150">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="fcfba-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="fcfba-151">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="fcfba-152">Dosyaları *Wwwroot/js/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="fcfba-153">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="fcfba-153">Copy only the specified files.</span></span>

  <span data-ttu-id="fcfba-154">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="fcfba-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-155">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fcfba-156">**Terminalde**, Libman 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="fcfba-157">Proje klasörüne gidin ( *Signalrchat. csproj* dosyasını içeren bir dosya).</span><span class="sxs-lookup"><span data-stu-id="fcfba-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="fcfba-158">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="fcfba-159">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="fcfba-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="fcfba-160">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="fcfba-161">Dosyaları *Wwwroot/js/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="fcfba-162">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="fcfba-162">Copy only the specified files.</span></span>

  <span data-ttu-id="fcfba-163">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="fcfba-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="fcfba-164">SignalR hub 'ı oluşturma</span><span class="sxs-lookup"><span data-stu-id="fcfba-164">Create a SignalR hub</span></span>

<span data-ttu-id="fcfba-165">*Hub* , istemci-sunucu iletişimini işleyen yüksek düzeyli bir işlem hattı görevi gören bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="fcfba-166">SignalRChat proje klasöründe bir *hub* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="fcfba-167">*Hub 'lar* klasöründe, aşağıdaki kodla bir *ChatHub.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="fcfba-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="fcfba-168">`ChatHub` Sınıfı, SignalR `Hub` sınıfından devralır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="fcfba-169">`Hub` Sınıfı bağlantıları, grupları ve mesajlaşmayı yönetir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="fcfba-170">Yöntemi `SendMessage` , tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="fcfba-171">Yöntemi çağıran JavaScript istemci kodu Öğreticinin ilerleyen kısımlarında gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="fcfba-172">SignalR kodu, maksimum ölçeklenebilirlik sağlamak için zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="fcfba-173">SignalR 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="fcfba-173">Configure SignalR</span></span>

<span data-ttu-id="fcfba-174">SignalR isteklerini SignalR 'ye iletmek için SignalR sunucusunun yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="fcfba-175">Aşağıdaki Vurgulanan kodu *Startup.cs* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="fcfba-176">Bu değişiklikler ASP.NET Core bağımlılığı ekleme ve yönlendirme sistemlerine SignalR ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="fcfba-177">SignalR istemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="fcfba-177">Add SignalR client code</span></span>

* <span data-ttu-id="fcfba-178">*Pages\ındex.cshtml* içindeki içeriği şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="fcfba-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="fcfba-179">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcfba-179">The preceding code:</span></span>

  * <span data-ttu-id="fcfba-180">Ad ve ileti metni ve Gönder düğmesi için metin kutuları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="fcfba-181">SignalR hub 'ından `id="messagesList"` alınan iletileri görüntülemek için içeren bir liste oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="fcfba-182">SignalR için betik başvurularını ve sonraki adımda oluşturduğunuz *chat. js* uygulama kodunu içerir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="fcfba-183">*Wwwroot/js* klasöründe, aşağıdaki kodla bir *chat. js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="fcfba-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="fcfba-184">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcfba-184">The preceding code:</span></span>

  * <span data-ttu-id="fcfba-185">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="fcfba-186">, Hub 'a ileti gönderen bir işleyiciye Gönder düğmesine ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="fcfba-187">, Hub 'dan iletileri alan ve bunları listeye ekleyen bir işleyici olan bağlantı nesnesine ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="fcfba-188">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="fcfba-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fcfba-190">Uygulamayı hata ayıklamadan çalıştırmak için **CTRL + F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fcfba-192">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fcfba-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-193">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fcfba-194">Menüden, **hata ayıklama olmadan başlat > Çalıştır**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="fcfba-195">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="fcfba-196">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Ileti gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="fcfba-197">Ad ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="fcfba-199">Uygulama işe yaramazsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="fcfba-200">HTML ve JavaScript kodunuzla ilgili hatalarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fcfba-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="fcfba-201">Örneğin, *SignalR. js* ' yi yönlendirenden farklı bir klasöre yerleştirdiğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="fcfba-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="fcfba-202">Bu durumda, bu dosyaya başvuru çalışmaz ve konsolunda 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="fcfba-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="fcfba-203">![SignalR. js bulunamadı hatası](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="fcfba-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="fcfba-204">Chrome 'da hata ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY alırsanız, geliştirme sertifikanızı güncelleştirmek için şu komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fcfba-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fcfba-205">Bu öğreticide, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="fcfba-206">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="fcfba-206">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="fcfba-207">Bir Web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-207">Create a web project.</span></span>   
> * <span data-ttu-id="fcfba-208">SignalR istemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-208">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="fcfba-209">Bir SignalR hub 'ı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-209">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="fcfba-210">Projeyi SignalR kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-210">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="fcfba-211">Herhangi bir istemciden tüm bağlı istemcilere ileti gönderen kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="fcfba-212">Sonunda, çalışan bir sohbet uygulamasına sahipsiniz: ![SignalR örnek uygulaması](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="fcfba-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="fcfba-213">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="fcfba-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-216">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="fcfba-217">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="fcfba-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="fcfba-219">Menüden **dosya > yeni proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="fcfba-220">**Yeni proje** iletişim kutusunda, **Visual C# > Web > ASP.NET Core Web uygulaması > yüklü**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="fcfba-221">Projeyi *Signalrchat*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-221">Name the project *SignalRChat*.</span></span> 

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="fcfba-223">Razor Pages kullanan bir proje oluşturmak için **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="fcfba-224">**.NET Core**'un hedef çerçevesini seçin, **ASP.NET Core 2,2**' i seçin ve **Tamam**' ı tıklatın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Visual Studio 'da yeni proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="fcfba-227">[Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) ' i yeni proje klasörünün oluşturulacağı klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="fcfba-228">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fcfba-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="fcfba-230">Menüden **dosya > yeni çözüm**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="fcfba-231">**.NET Core > App > ASP.NET Core Web uygulaması** ' nı ( **ASP.NET Core Web uygulaması (MVC)** seçmeyin) seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="fcfba-232">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-232">Select **Next**.</span></span>  

* <span data-ttu-id="fcfba-233">Projeyi *Signalrchat*olarak adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="fcfba-234">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="fcfba-234">Add the SignalR client library</span></span>   

<span data-ttu-id="fcfba-235">SignalR sunucu kitaplığı, `Microsoft.AspNetCore.App` metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="fcfba-236">JavaScript istemci kitaplığı projeye otomatik olarak dahil değildir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="fcfba-237">Bu öğreticide, istemci kitaplığını *unpkg*'den almak Için kitaplık Yöneticisi 'Ni (Libman) kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="fcfba-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="fcfba-238">unpkg, Node. js Paket Yöneticisi NPM 'de bulunan her şeyi teslim edebilen bir içerik teslim ağı (CDN).</span><span class="sxs-lookup"><span data-stu-id="fcfba-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="fcfba-240">**Çözüm Gezgini**' de projeye sağ tıklayın ve **istemci tarafı kitaplığı** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="fcfba-241">**Istemci tarafı kitaplığı Ekle** Iletişim kutusunda **sağlayıcı** için **unpkg**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="fcfba-242">**Kitaplık**için, öğesini `@microsoft/signalr@3`girin ve Önizleme olmayan en son sürümü seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-kitaplık Seç](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="fcfba-244">**Belirli dosyaları seç**' i seçin, *dağ/Browser* klasörünü genişletin ve *SignalR. js* ve *SignalR. min. js*' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="fcfba-245">**Hedef konumu** *Wwwroot/lib/SignalR/* olarak ayarlayın ve **yüklemeyi**seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Istemci tarafı kitaplığı Ekle iletişim kutusu-dosya ve hedef seçme](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="fcfba-247">LibMan, bir *Wwwroot/LIB/SignalR* klasörü oluşturur ve seçilen dosyaları buna kopyalar.</span><span class="sxs-lookup"><span data-stu-id="fcfba-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="fcfba-249">Tümleşik terminalde, LibMan 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="fcfba-250">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="fcfba-251">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-251">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="fcfba-252">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="fcfba-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="fcfba-253">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="fcfba-254">Dosyaları *Wwwroot/lib/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="fcfba-255">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="fcfba-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="fcfba-256">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="fcfba-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-257">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="fcfba-258">**Terminalde**, Libman 'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="fcfba-259">Proje klasörüne gidin ( *Signalrchat. csproj* dosyasını içeren bir dosya).</span><span class="sxs-lookup"><span data-stu-id="fcfba-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="fcfba-260">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-260">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="fcfba-261">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="fcfba-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="fcfba-262">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="fcfba-263">Dosyaları *Wwwroot/lib/SignalR* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="fcfba-264">Yalnızca belirtilen dosyaları kopyala.</span><span class="sxs-lookup"><span data-stu-id="fcfba-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="fcfba-265">Çıktı aşağıdaki örneğe benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="fcfba-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="fcfba-266">SignalR hub 'ı oluşturma</span><span class="sxs-lookup"><span data-stu-id="fcfba-266">Create a SignalR hub</span></span> 

<span data-ttu-id="fcfba-267">*Hub* , istemci-sunucu iletişimini işleyen yüksek düzeyli bir işlem hattı görevi gören bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="fcfba-268">SignalRChat proje klasöründe bir *hub* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcfba-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="fcfba-269">*Hub 'lar* klasöründe, aşağıdaki kodla bir *ChatHub.cs* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="fcfba-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="fcfba-270">`ChatHub` Sınıfı, SignalR `Hub` sınıfından devralır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="fcfba-271">`Hub` Sınıfı bağlantıları, grupları ve mesajlaşmayı yönetir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-271">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="fcfba-272">Yöntemi `SendMessage` , tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="fcfba-273">Yöntemi çağıran JavaScript istemci kodu Öğreticinin ilerleyen kısımlarında gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="fcfba-274">SignalR kodu, maksimum ölçeklenebilirlik sağlamak için zaman uyumsuzdur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-274">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="fcfba-275">SignalR 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="fcfba-275">Configure SignalR</span></span>    

<span data-ttu-id="fcfba-276">SignalR isteklerini SignalR 'ye iletmek için SignalR sunucusunun yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="fcfba-277">Aşağıdaki Vurgulanan kodu *Startup.cs* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="fcfba-278">Bu değişiklikler ASP.NET Core bağımlılığı ekleme sistemine ve ara yazılım ardışık düzenine SignalR ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="fcfba-279">SignalR istemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="fcfba-279">Add SignalR client code</span></span>  

* <span data-ttu-id="fcfba-280">*Pages\ındex.cshtml* içindeki içeriği şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="fcfba-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="fcfba-281">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcfba-281">The preceding code:</span></span>   

  * <span data-ttu-id="fcfba-282">Ad ve ileti metni ve Gönder düğmesi için metin kutuları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="fcfba-283">SignalR hub 'ından `id="messagesList"` alınan iletileri görüntülemek için içeren bir liste oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fcfba-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="fcfba-284">SignalR için betik başvurularını ve sonraki adımda oluşturduğunuz *chat. js* uygulama kodunu içerir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="fcfba-285">*Wwwroot/js* klasöründe, aşağıdaki kodla bir *chat. js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="fcfba-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="fcfba-286">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcfba-286">The preceding code:</span></span>   

  * <span data-ttu-id="fcfba-287">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="fcfba-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="fcfba-288">, Hub 'a ileti gönderen bir işleyiciye Gönder düğmesine ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="fcfba-289">, Hub 'dan iletileri alan ve bunları listeye ekleyen bir işleyici olan bağlantı nesnesine ekler.</span><span class="sxs-lookup"><span data-stu-id="fcfba-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="fcfba-290">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="fcfba-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="fcfba-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="fcfba-292">Uygulamayı hata ayıklamadan çalıştırmak için **CTRL + F5** tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="fcfba-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fcfba-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="fcfba-294">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fcfba-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fcfba-295">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcfba-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fcfba-296">Menüden, **hata ayıklama olmadan başlat > Çalıştır**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="fcfba-297">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcfba-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="fcfba-298">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Ileti gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="fcfba-299">Ad ve ileti anında her iki sayfada da görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="fcfba-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="fcfba-300">![SignalRörnek uygulama](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="fcfba-300">![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="fcfba-301">Uygulama işe yaramazsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="fcfba-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="fcfba-302">HTML ve JavaScript kodunuzla ilgili hatalarla karşılaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fcfba-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="fcfba-303">Örneğin, *SignalR. js* ' yi yönlendirenden farklı bir klasöre yerleştirdiğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="fcfba-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="fcfba-304">Bu durumda, bu dosyaya başvuru çalışmaz ve konsolunda 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="fcfba-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="fcfba-305">![SignalR. js bulunamadı hatası](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="fcfba-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="fcfba-306">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fcfba-306">Additional resources</span></span> 
* [<span data-ttu-id="fcfba-307">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="fcfba-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
