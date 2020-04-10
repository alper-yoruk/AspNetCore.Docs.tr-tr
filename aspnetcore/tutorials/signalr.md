---
title: ASP.NET Core ile başlayınSignalR
author: bradygaster
description: Bu öğreticide, Core'ASP.NET SignalRkullanan bir sohbet uygulaması oluşturursunuz.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr
ms.openlocfilehash: 869eb325ee95a78e4b16c61c5b0573bb094292e3
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994616"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="a6ee8-103">Öğretici: ASP.NET Core SignalR ile başlayın</span><span class="sxs-lookup"><span data-stu-id="a6ee8-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6ee8-104">Bu öğretici, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a6ee8-105">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6ee8-106">Bir web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-106">Create a web project.</span></span>
> * <span data-ttu-id="a6ee8-107">SignalR istemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="a6ee8-108">Bir SignalR hub'ı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="a6ee8-109">Projeyi SignalR kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="a6ee8-110">Bağlı tüm istemcilere herhangi bir istemciden ileti gönderen kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="a6ee8-111">Sonunda, çalışan bir sohbet uygulamanız olur:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-111">At the end, you'll have a working chat app:</span></span>

![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="a6ee8-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="a6ee8-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-116">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="a6ee8-117">Bir web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a6ee8-119">Menüden Dosya **> Yeni Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="a6ee8-120">Yeni **bir proje oluştur** iletişim **kutusunda, Çekirdek Web UygulamasıASP.NET'nu**seçin ve ardından **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="a6ee8-121">Yeni proje iletişim **günlüğüne Yapıla,** project *SignalRChat'i*adlandırın ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="a6ee8-122">Yeni **bir ASP.NET Core web Uygulaması oluştur** iletişim kutusunda **.NET Core** ve ASP.NET Core **3.0'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="a6ee8-123">Razor Pages kullanan bir proje oluşturmak için **Web Uygulaması'nı** seçin ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a6ee8-126">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal) yeni proje klasöründeki klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="a6ee8-127">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-128">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6ee8-129">Menüden Dosya **> Yeni Çözüm'ünü**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="a6ee8-130">**.NET Core > App > Web Application** **(Web Uygulaması (Model-View-Controller)** seçeneğini seçmeyin) seçeneğini belirleyin ve sonra **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="a6ee8-131">**Hedef Çerçevenin** **.NET Core 3.0**olarak ayarlandığından emin olun ve sonra **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="a6ee8-132">Projeye *SignalRChat*adını ver ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a6ee8-133">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="a6ee8-133">Add the SignalR client library</span></span>

<span data-ttu-id="a6ee8-134">SignalR sunucu kitaplığı, Core 3.0 paylaşılan ASP.NET çerçevesine dahildir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="a6ee8-135">JavaScript istemci kitaplığı projeye otomatik olarak dahil edilemiyor.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a6ee8-136">Bu öğretici için, istemci kitaplığını *unpkg'dan*almak için Kitaplık Yöneticisi'ni (LibMan) kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a6ee8-137">unpkg npm, Node.js paket yöneticisi bulunan bir şey teslim edebilirsiniz bir içerik dağıtım ağı (CDN) olduğunu.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="a6ee8-139">**Çözüm Gezgini'nde,** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="a6ee8-140">**İstemci Tarafı Kitaplığı Ekle** iletişim kutusunda, **Sağlayıcı** **unpkg'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="a6ee8-141">**Kitaplık**için `@microsoft/signalr@latest`, girin .</span><span class="sxs-lookup"><span data-stu-id="a6ee8-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="a6ee8-142">**Belirli dosyaları seçin,** *dist/tarayıcı* klasörünü genişletin ve *signalr.js* ve *signalr.min.js'i*seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="a6ee8-143">**Hedef Konumu** *wwwroot/js/signalr/* olarak ayarlayın ve **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![İstemci-Side Kitaplığı ekle iletişim kutusu - kitaplık seçin](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="a6ee8-145">LibMan bir *wwwroot/js/signalr* klasörü oluşturur ve seçilen dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="a6ee8-147">Tümleşik terminalde, LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a6ee8-148">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a6ee8-149">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a6ee8-150">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a6ee8-151">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a6ee8-152">Dosyaları *wwwroot/js/signalr* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a6ee8-153">Yalnızca belirtilen dosyaları kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-153">Copy only the specified files.</span></span>

  <span data-ttu-id="a6ee8-154">Çıktı aşağıdaki örnek gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-155">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6ee8-156">**Terminalde,** LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="a6ee8-157">Proje klasörüne *(SignalRChat.csproj* dosyasını içeren dosya) gidin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="a6ee8-158">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="a6ee8-159">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="a6ee8-160">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="a6ee8-161">Dosyaları *wwwroot/js/signalr* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="a6ee8-162">Yalnızca belirtilen dosyaları kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-162">Copy only the specified files.</span></span>

  <span data-ttu-id="a6ee8-163">Çıktı aşağıdaki örnek gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="a6ee8-164">SignalR hub'ı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-164">Create a SignalR hub</span></span>

<span data-ttu-id="a6ee8-165">*Hub,* istemci-sunucu iletişimini işleyen üst düzey bir ardışık hatlar olarak hizmet veren bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="a6ee8-166">SignalRChat proje klasöründe bir *Hubs* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="a6ee8-167">*Hub'lar* klasöründe, aşağıdaki koda sahip *ChatHub.cs* bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="a6ee8-168">Sınıf `ChatHub` SinyalR `Hub` sınıfından devralır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a6ee8-169">Sınıf `Hub` bağlantıları, grupları ve iletileri yönetir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="a6ee8-170">Yöntem, `SendMessage` tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a6ee8-171">Yöntemi çağıran JavaScript istemci kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a6ee8-172">SignalR kodu maksimum ölçeklenebilirlik sağlamak için asynchronous olduğunu.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="a6ee8-173">SignalR'i yapılandır</span><span class="sxs-lookup"><span data-stu-id="a6ee8-173">Configure SignalR</span></span>

<span data-ttu-id="a6ee8-174">SignalR sunucusu SignalR isteklerini SignalR'a iletecek şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="a6ee8-175">*Startup.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="a6ee8-176">Bu değişiklikler, ASP.NET Çekirdek bağımlılık enjeksiyon ve yönlendirme sistemlerine SignalR ekler.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="a6ee8-177">SignalR istemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="a6ee8-177">Add SignalR client code</span></span>

* <span data-ttu-id="a6ee8-178">*Pages\Index.cshtml'deki* içeriği aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="a6ee8-179">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-179">The preceding code:</span></span>

  * <span data-ttu-id="a6ee8-180">Ad ve ileti metni için metin kutuları ve gönder düğmesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="a6ee8-181">SignalR hub'ından alınan iletileri görüntülemek `id="messagesList"` için bir liste oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="a6ee8-182">SignalR'a ve bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna komut dosyası başvurularını içerir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="a6ee8-183">*wwwroot/js* klasöründe, aşağıdaki kodu içeren bir *chat.js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="a6ee8-184">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-184">The preceding code:</span></span>

  * <span data-ttu-id="a6ee8-185">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="a6ee8-186">Gönder düğmesine, hub'a ileti gönderen bir işleyici ekler.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="a6ee8-187">Bağlantı nesnesine hub'dan ileti alan ve bunları listeye ekleyen bir işleyici ekler.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="a6ee8-188">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6ee8-190">Hata ayıklama olmadan uygulamayı çalıştırmak için **CTRL+F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a6ee8-192">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-193">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6ee8-194">Menüden, **Hata Ayıklama olmadan Çalıştır > Başlat'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a6ee8-195">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a6ee8-196">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **İleti Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="a6ee8-197">Ad ve ileti her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR örnek uygulaması](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="a6ee8-199">Uygulama çalışmıyorsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a6ee8-200">HTML ve JavaScript kodunuzla ilgili hatalar görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a6ee8-201">Örneğin, *signalr.js'yi* yönlendirilmiş ten farklı bir klasöre koyduğunuzu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a6ee8-202">Bu durumda, bu dosyaya yapılan başvuru çalışmaz ve konsolda bir 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="a6ee8-203">![signalr.js hata bulunamadı](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a6ee8-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="a6ee8-204">Chrome'da ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY hata alırsanız, geliştirme sertifikanızı güncelleştirmek için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6ee8-205">Bu öğretici, SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="a6ee8-206">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-206">You learn how to:</span></span>   

> [!div class="checklist"]  
> * <span data-ttu-id="a6ee8-207">Bir web projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-207">Create a web project.</span></span>   
> * <span data-ttu-id="a6ee8-208">SignalR istemci kitaplığını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-208">Add the SignalR client library.</span></span> 
> * <span data-ttu-id="a6ee8-209">Bir SignalR hub'ı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-209">Create a SignalR hub.</span></span>   
> * <span data-ttu-id="a6ee8-210">Projeyi SignalR kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-210">Configure the project to use SignalR.</span></span>   
> * <span data-ttu-id="a6ee8-211">Bağlı tüm istemcilere herhangi bir istemciden ileti gönderen kod ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="a6ee8-212">Sonunda, çalışan bir sohbet uygulaması olacak: ![SignalR örnek uygulaması](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="a6ee8-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

## <a name="prerequisites"></a><span data-ttu-id="a6ee8-213">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="a6ee8-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-216">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="a6ee8-217">Web projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a6ee8-219">Menüden Dosya **> Yeni Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="a6ee8-220">Yeni **Proje** iletişim kutusunda, **Web > ASP.NET Core Web Uygulaması > Yüklü > Visual C# ASP.NET'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a6ee8-221">Proje *SignalRChat*adı .</span><span class="sxs-lookup"><span data-stu-id="a6ee8-221">Name the project *SignalRChat*.</span></span> 

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="a6ee8-223">Razor Pages kullanan bir proje oluşturmak için **Web Uygulaması'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-223">Select **Web Application** to create a project that uses Razor Pages.</span></span> 

* <span data-ttu-id="a6ee8-224">**.NET Core'un**hedef çerçevesini seçin, **Core 2.2ASP.NET**seçin ve **Tamam'ı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Visual Studio'da Yeni Proje iletişim kutusu](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a6ee8-227">[Tümleşik terminali](https://code.visualstudio.com/docs/editor/integrated-terminal) yeni proje klasöründeki klasöre açın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="a6ee8-228">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a6ee8-230">Menüden Dosya **> Yeni Çözüm'ünü**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="a6ee8-231">**ASP.NET Core Web App > .NET Core > App'ı** seçin **(ASP.NET Çekirdek Web Uygulaması (MVC)** seçmeyin).</span><span class="sxs-lookup"><span data-stu-id="a6ee8-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="a6ee8-232">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-232">Select **Next**.</span></span>  

* <span data-ttu-id="a6ee8-233">Projeye *SignalRChat*adını ver ve ardından **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="a6ee8-234">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="a6ee8-234">Add the SignalR client library</span></span>   

<span data-ttu-id="a6ee8-235">SignalR sunucu kitaplığı meta `Microsoft.AspNetCore.App` pakete dahildir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="a6ee8-236">JavaScript istemci kitaplığı projeye otomatik olarak dahil edilemiyor.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="a6ee8-237">Bu öğretici için, istemci kitaplığını *unpkg'dan*almak için Kitaplık Yöneticisi'ni (LibMan) kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="a6ee8-238">unpkg npm, Node.js paket yöneticisi bulunan bir şey teslim edebilirsiniz bir içerik dağıtım ağı (CDN) olduğunu.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="a6ee8-240">**Çözüm Gezgini'nde,** projeyi sağ tıklatın ve **İstemci Tarafı Kitaplığı** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="a6ee8-241">**İstemci Tarafı Kitaplığı Ekle** iletişim kutusunda, **Sağlayıcı** **unpkg'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="a6ee8-242">**Kitaplık**için, önizleme olmayan en son sürümü girin `@microsoft/signalr@3`ve seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![İstemci-Side Kitaplığı ekle iletişim kutusu - kitaplık seçin](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="a6ee8-244">**Belirli dosyaları seçin,** *dist/tarayıcı* klasörünü genişletin ve *signalr.js* ve *signalr.min.js'i*seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="a6ee8-245">**Hedef Konumu** *wwwroot/lib/signalr/* olarak ayarlayın ve **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![İstemci Tarafı Kitaplığı iletişim kutusu ekle - dosyaları ve hedef seçin](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="a6ee8-247">LibMan bir *wwwroot/lib/signalr* klasörü oluşturur ve seçili dosyaları kopyalar.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="a6ee8-249">Tümleşik terminalde, LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a6ee8-250">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="a6ee8-251">Çıktıyı görmeden önce birkaç saniye beklemeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-251">You might have to wait a few seconds before seeing output.</span></span>   

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="a6ee8-252">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a6ee8-253">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a6ee8-254">Dosyaları *wwwroot/lib/signalr* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a6ee8-255">Yalnızca belirtilen dosyaları kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="a6ee8-256">Çıktı aşağıdaki örnek gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-257">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="a6ee8-258">**Terminalde,** LibMan'ı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="a6ee8-259">Proje klasörüne *(SignalRChat.csproj* dosyasını içeren dosya) gidin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="a6ee8-260">LibMan kullanarak SignalR istemci kitaplığını almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-260">Run the following command to get the SignalR client library by using LibMan.</span></span>  

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="a6ee8-261">Parametreler aşağıdaki seçenekleri belirtir:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="a6ee8-262">Unpkg sağlayıcısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="a6ee8-263">Dosyaları *wwwroot/lib/signalr* hedefine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="a6ee8-264">Yalnızca belirtilen dosyaları kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="a6ee8-265">Çıktı aşağıdaki örnek gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="a6ee8-266">SignalR hub'ı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-266">Create a SignalR hub</span></span> 

<span data-ttu-id="a6ee8-267">*Hub,* istemci-sunucu iletişimini işleyen üst düzey bir ardışık hatlar olarak hizmet veren bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="a6ee8-268">SignalRChat proje klasöründe bir *Hubs* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="a6ee8-269">*Hub'lar* klasöründe, aşağıdaki koda sahip *ChatHub.cs* bir dosya oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="a6ee8-270">Sınıf `ChatHub` SinyalR `Hub` sınıfından devralır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="a6ee8-271">Sınıf `Hub` bağlantıları, grupları ve iletileri yönetir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-271">The `Hub` class manages connections, groups, and messaging.</span></span>    

  <span data-ttu-id="a6ee8-272">Yöntem, `SendMessage` tüm istemcilere ileti göndermek için bağlı bir istemci tarafından çağrılabilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="a6ee8-273">Yöntemi çağıran JavaScript istemci kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="a6ee8-274">SignalR kodu maksimum ölçeklenebilirlik sağlamak için asynchronous olduğunu.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-274">SignalR code is asynchronous to provide maximum scalability.</span></span>  

## <a name="configure-signalr"></a><span data-ttu-id="a6ee8-275">SignalR'i yapılandır</span><span class="sxs-lookup"><span data-stu-id="a6ee8-275">Configure SignalR</span></span>    

<span data-ttu-id="a6ee8-276">SignalR sunucusu SignalR isteklerini SignalR'a iletecek şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>  

* <span data-ttu-id="a6ee8-277">*Startup.cs* dosyasına aşağıdaki vurgulanmış kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="a6ee8-278">Bu değişiklikler, Core bağımlılık enjeksiyon sistemi ve ara yazılım boru hattı ASP.NET SignalR ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>    

## <a name="add-signalr-client-code"></a><span data-ttu-id="a6ee8-279">SignalR istemci kodu ekle</span><span class="sxs-lookup"><span data-stu-id="a6ee8-279">Add SignalR client code</span></span>  

* <span data-ttu-id="a6ee8-280">*Pages\Index.cshtml'deki* içeriği aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="a6ee8-281">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-281">The preceding code:</span></span>   

  * <span data-ttu-id="a6ee8-282">Ad ve ileti metni için metin kutuları ve gönder düğmesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="a6ee8-283">SignalR hub'ından alınan iletileri görüntülemek `id="messagesList"` için bir liste oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span> 
  * <span data-ttu-id="a6ee8-284">SignalR'a ve bir sonraki adımda oluşturduğunuz *chat.js* uygulama koduna komut dosyası başvurularını içerir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>  

* <span data-ttu-id="a6ee8-285">*wwwroot/js* klasöründe, aşağıdaki kodu içeren bir *chat.js* dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="a6ee8-286">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-286">The preceding code:</span></span>   

  * <span data-ttu-id="a6ee8-287">Bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="a6ee8-288">Gönder düğmesine, hub'a ileti gönderen bir işleyici ekler.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="a6ee8-289">Bağlantı nesnesine hub'dan ileti alan ve bunları listeye ekleyen bir işleyici ekler.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="a6ee8-290">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a6ee8-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="a6ee8-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="a6ee8-292">Hata ayıklama olmadan uygulamayı çalıştırmak için **CTRL+F5** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6ee8-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6ee8-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="a6ee8-294">Tümleşik terminalde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a6ee8-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6ee8-295">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6ee8-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6ee8-296">Menüden, **Hata Ayıklama olmadan Çalıştır > Başlat'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="a6ee8-297">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="a6ee8-298">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **İleti Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="a6ee8-299">Ad ve ileti her iki sayfada anında görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="a6ee8-300">![SignalRörnek uygulama](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="a6ee8-300">![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="a6ee8-301">Uygulama çalışmıyorsa, tarayıcı geliştirici araçlarınızı (F12) açın ve konsola gidin.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="a6ee8-302">HTML ve JavaScript kodunuzla ilgili hatalar görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="a6ee8-303">Örneğin, *signalr.js'yi* yönlendirilmiş ten farklı bir klasöre koyduğunuzu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="a6ee8-304">Bu durumda, bu dosyaya yapılan başvuru çalışmaz ve konsolda bir 404 hatası görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="a6ee8-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="a6ee8-305">![signalr.js hata bulunamadı](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="a6ee8-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="a6ee8-306">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a6ee8-306">Additional resources</span></span> 
* [<span data-ttu-id="a6ee8-307">Bu öğretici Youtube sürümü</span><span class="sxs-lookup"><span data-stu-id="a6ee8-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
