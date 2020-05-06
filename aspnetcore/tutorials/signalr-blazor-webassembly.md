---
title: SignalR Webassembly Blazor ile ASP.NET Core kullanma
author: guardrex
description: Webassembly ile SignalR Blazor ASP.NET Core kullanan bir sohbet uygulaması oluşturun.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773794"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="381c1-103">Blazor WebAssembly ile ASP.NET Core SignalR kullanma</span><span class="sxs-lookup"><span data-stu-id="381c1-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="381c1-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="381c1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="381c1-105">Bu öğreticide, Blazor WebAssembly ile SignalR kullanarak gerçek zamanlı bir uygulama oluşturmanın temelleri öğretilir.</span><span class="sxs-lookup"><span data-stu-id="381c1-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="381c1-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="381c1-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="381c1-107">Blazor WebAssembly barındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="381c1-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="381c1-108">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="381c1-109">SignalR hub 'ı ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="381c1-110">SignalR hub 'ı için SignalR Hizmetleri ve uç nokta ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="381c1-111">Sohbet için Razor bileşeni kodu ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-111">Add Razor component code for chat</span></span>

<span data-ttu-id="381c1-112">Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="381c1-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="381c1-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="381c1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="381c1-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="381c1-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="381c1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="381c1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="381c1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="381c1-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="381c1-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="381c1-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="381c1-119">Barındırılan Blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="381c1-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="381c1-120">Visual Studio sürüm 16,6 Preview 2 veya sonraki bir sürümü kullanmadığınız durumlarda, [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) şablonunu ' nı yükledikten sonra.</span><span class="sxs-lookup"><span data-stu-id="381c1-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="381c1-121">[Microsoft. AspNetCore. components. WebAssembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) paketinin önizleme sürümü vardır, ancak Blazor WebAssembly önizlemededir.</span><span class="sxs-lookup"><span data-stu-id="381c1-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="381c1-122">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

<span data-ttu-id="381c1-123">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="381c1-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="381c1-125">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="381c1-125">Create a new project.</span></span>

1. <span data-ttu-id="381c1-126">**Blazor uygulamasını** seçin ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="381c1-127">**Proje adı** alanına "BlazorSignalRApp" yazın.</span><span class="sxs-lookup"><span data-stu-id="381c1-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="381c1-128">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="381c1-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="381c1-129">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-129">Select **Create**.</span></span>

1. <span data-ttu-id="381c1-130">**Blazor WebAssembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="381c1-131">**Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="381c1-132">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="381c1-133">Visual Studio 'nun yeni bir sürümünü yükselttiyseniz veya yüklediyseniz ve Blazor WebAssembly şablonu VS Kullanıcı arabiriminde görünmüyorsa, daha önce gösterilen `dotnet new` komutu kullanarak şablonu yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="381c1-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="381c1-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="381c1-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="381c1-135">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="381c1-136">Visual Studio Code, uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="381c1-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="381c1-137">Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="381c1-138">Visual Studio Code, *. vscode* klasörünü oluşturulan *Launch. JSON* ve *Tasks. JSON* dosyaları ile otomatik olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="381c1-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="381c1-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="381c1-140">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="381c1-141">Mac için Visual Studio, proje klasörüne gidip projenin çözüm dosyasını (*. sln*) açarak projeyi açın.</span><span class="sxs-lookup"><span data-stu-id="381c1-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="381c1-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="381c1-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="381c1-143">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="381c1-144">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="381c1-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="381c1-146">**Çözüm Gezgini**, **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="381c1-147">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** *NuGet.org*olarak ayarlandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="381c1-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="381c1-148">Araştır **seçiliyken,** arama kutusuna "Microsoft. aspnetcore. SignalR. Client" yazın.</span><span class="sxs-lookup"><span data-stu-id="381c1-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="381c1-149">Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketi seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="381c1-150">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="381c1-151">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="381c1-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="381c1-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="381c1-153">**Tümleşik terminalde** (araç çubuğundan**Görünüm** > **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="381c1-154">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="381c1-155">**Çözüm** kenar çubuğunda **BlazorSignalRApp. Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="381c1-156">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun *NuGet.org*olarak ayarlandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="381c1-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="381c1-157">Araştır **seçiliyken,** arama kutusuna "Microsoft. aspnetcore. SignalR. Client" yazın.</span><span class="sxs-lookup"><span data-stu-id="381c1-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="381c1-158">Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketin yanındaki onay kutusunu Işaretleyin ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="381c1-159">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="381c1-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="381c1-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="381c1-161">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="381c1-162">SignalR hub 'ı ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-162">Add a SignalR hub</span></span>

<span data-ttu-id="381c1-163">**BlazorSignalRApp. Server** projesinde, bir *hub* (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı (*hub/ChatHub. cs*) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="381c1-164">SignalR hub 'ı için hizmetler ve uç nokta ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="381c1-165">**BlazorSignalRApp. Server** projesinde, *Startup.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="381c1-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="381c1-166">`ChatHub` Sınıfın ad alanını dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="381c1-167">SignalR ve yanıt sıkıştırma ara yazılım hizmetlerini şu `Startup.ConfigureServices`şekilde ekleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="381c1-168">Denetleyiciler `Startup.Configure` ve istemci tarafı geri dönüş uç noktaları arasında, Hub için bir uç nokta ekleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="381c1-169">Sohbet için Razor bileşeni kodu ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="381c1-170">**BlazorSignalRApp. Client** projesinde *Pages/Index. Razor* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="381c1-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="381c1-171">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="381c1-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="381c1-172">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="381c1-172">Run the app</span></span>

1. <span data-ttu-id="381c1-173">Araç kılavuzunuz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="381c1-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="381c1-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="381c1-175">**Çözüm Gezgini**, **BlazorSignalRApp. Server** projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="381c1-176">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="381c1-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="381c1-177">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="381c1-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="381c1-178">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="381c1-179">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="381c1-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="381c1-181">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="381c1-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="381c1-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="381c1-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="381c1-183">VS Code, sunucu uygulaması (*. vscode/Launch. JSON*) için bir başlatma profili oluşturmak üzere teklif edildiğinde, `program` uygulamanın derlemesini (`{APPLICATION NAME}.Server.dll`) göstermek için aşağıdakine benzer şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="381c1-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="381c1-184">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="381c1-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="381c1-185">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="381c1-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="381c1-186">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="381c1-187">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="381c1-187">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="381c1-189">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="381c1-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="381c1-190">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="381c1-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="381c1-191">**Çözüm** kenar çubuğunda **BlazorSignalRApp. Server** projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="381c1-192">Uygulamayı hata ayıklamadan çalıştırmak için hata ayıklama veya <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> ile çalıştırmak için <kbd>⌘</kbd>+<kbd>↩</kbd>\* \* tuşlarına basın.</span><span class="sxs-lookup"><span data-stu-id="381c1-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="381c1-193">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="381c1-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="381c1-194">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="381c1-195">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="381c1-195">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="381c1-197">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="381c1-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="381c1-198">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="381c1-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="381c1-199">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="381c1-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="381c1-200">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="381c1-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="381c1-201">Tarayıcı ' yı seçin, bir ad ve ileti girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="381c1-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="381c1-202">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="381c1-202">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="381c1-204">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="381c1-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="381c1-205">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="381c1-205">Next steps</span></span>

<span data-ttu-id="381c1-206">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="381c1-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="381c1-207">Blazor Webassembly barındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="381c1-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="381c1-208">SignalR İstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="381c1-209">SignalR Hub ekleme</span><span class="sxs-lookup"><span data-stu-id="381c1-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="381c1-210">Hub için hizmetler ve uç nokta ekleme SignalR SignalR</span><span class="sxs-lookup"><span data-stu-id="381c1-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="381c1-211">Sohbet Razor için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="381c1-211">Add Razor component code for chat</span></span>

<span data-ttu-id="381c1-212">Uygulama oluşturma Blazor hakkında daha fazla bilgi edinmek için Blazor belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="381c1-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="381c1-213">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="381c1-213">Additional resources</span></span>

* <xref:signalr/introduction>
