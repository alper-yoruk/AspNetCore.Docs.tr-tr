---
title: WebAssembly SignalR ile Blazor ASP.NET Core kullanın
author: guardrex
description: WebAssembly ile Blazor ASP.NET Core SignalR kullanan bir sohbet uygulaması oluşturun.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 03db8b48bdacec1d6877a4ea09f97c242761c42d
ms.sourcegitcommit: f976dce28ad887bbd31720c318fd4a97cf96cc6d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81738018"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="0ee3e-103">Blazor WebAssembly ile ASP.NET Core SignalR kullanın</span><span class="sxs-lookup"><span data-stu-id="0ee3e-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="0ee3e-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ee3e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0ee3e-105">Bu öğretici Blazor WebAssembly ile SignalR kullanarak gerçek zamanlı bir uygulama oluşturma temellerini öğretir.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="0ee3e-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ee3e-107">Blazor WebAssembly Barındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ee3e-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0ee3e-108">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="0ee3e-109">SignalR hub'ı ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="0ee3e-110">SignalR hizmetleri ve SignalR hub'ı için bir uç nokta ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0ee3e-111">Sohbet için Razor bileşen kodu ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-111">Add Razor component code for chat</span></span>

<span data-ttu-id="0ee3e-112">Bu eğitimin sonunda, çalışan bir sohbet uygulamanız olacak.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0ee3e-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ee3e-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0ee3e-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0ee3e-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ee3e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ee3e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ee3e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ee3e-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0ee3e-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0ee3e-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="0ee3e-119">Barındırılan blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ee3e-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="0ee3e-120">Visual Studio sürüm 16.6 Preview 2 veya sonraki sürümlerini kullanmadığınızda [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) şablonu'nu yükleyin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="0ee3e-121">[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) paketi, Blazor WebAssembly önizlemedeyken bir önizleme sürümüne sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="0ee3e-122">Komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
```

<span data-ttu-id="0ee3e-123">Araç seçiminiz için kılavuzu izleyin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ee3e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0ee3e-125">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-125">Create a new project.</span></span>

1. <span data-ttu-id="0ee3e-126">**Blazor Uygulamasını** seçin ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="0ee3e-127">**Proje adı** alanına "BlazorSignalRApp" yazın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="0ee3e-128">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0ee3e-129">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-129">Select **Create**.</span></span>

1. <span data-ttu-id="0ee3e-130">**Blazor WebAssembly App** şablonu seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="0ee3e-131">**Advanced**altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0ee3e-132">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="0ee3e-133">Visual Studio'nun yeni bir sürümünü yükselttiyseniz veya yüklediyseniz ve Blazor WebAssembly şablonu VS UI'de görünmüyorsa, şablonu daha önce gösterilen komutu `dotnet new` kullanarak yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ee3e-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ee3e-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0ee3e-135">Komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0ee3e-136">Visual Studio Code'da uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0ee3e-137">İletişim kutusu, uygulamayı oluşturmak ve hata ayıklamak için varlık eklemek için göründüğünde **Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0ee3e-138">Visual Studio Code otomatik olarak oluşturulan *launch.json ve tasks.json* dosyaları ile *.vscode* klasörünü ekler. *tasks.json*</span><span class="sxs-lookup"><span data-stu-id="0ee3e-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ee3e-139">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ee3e-140">Komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0ee3e-141">Mac için Visual Studio'da, proje klasörüne gidip projenin çözüm dosyasını açarak projeyi açın (*.sln*).</span><span class="sxs-lookup"><span data-stu-id="0ee3e-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ee3e-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0ee3e-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0ee3e-143">Komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="0ee3e-144">SignalR istemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ee3e-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0ee3e-146">**Solution Explorer'da** **BlazorSignalRApp.Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0ee3e-147">**NuGet Paketlerini Yönet** iletişim kutusunda, **Paket kaynağının** *nuget.org*ayarlı olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0ee3e-148">**Gözat** seçili ile arama kutusuna "Microsoft.AspNetCore.SignalR.Client" yazın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0ee3e-149">Arama sonuçlarında `Microsoft.AspNetCore.SignalR.Client` paketi seçin ve **Yükle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="0ee3e-150">Önizleme **Değişiklikleri** iletişim kutusu görünüyorsa, **Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0ee3e-151">Lisans **Kabul** iletişim kutusu görünürse, lisans koşullarını kabul ediyorsanız **Kabul Et'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ee3e-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ee3e-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0ee3e-153">**Tümleşik Terminalde** (Araç çubuğundan**Terminali** **Görüntüle)** > aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ee3e-154">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ee3e-155">**Çözüm** kenar çubuğunda **BlazorSignalRApp.Client** projesine sağ tıklayın ve **NuGet Paketlerini Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0ee3e-156">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılır bırakmanın *nuget.org*olarak ayarlı olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0ee3e-157">**Gözat** seçili ile arama kutusuna "Microsoft.AspNetCore.SignalR.Client" yazın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0ee3e-158">Arama sonuçlarında, `Microsoft.AspNetCore.SignalR.Client` paketin yanındaki onay kutusunu seçin ve Paket **Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="0ee3e-159">Lisans **Kabul** iletişim kutusu görünürse, lisans koşullarını kabul ediyorsanız **Kabul Et'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ee3e-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0ee3e-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0ee3e-161">Komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="0ee3e-162">SignalR hub'ı ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-162">Add a SignalR hub</span></span>

<span data-ttu-id="0ee3e-163">**BlazorSignalRApp.Server** projesinde, hub *(çoğul)* klasörü oluşturun `ChatHub` ve aşağıdaki sınıfı ekleyin *(Hubs/ChatHub.cs):*</span><span class="sxs-lookup"><span data-stu-id="0ee3e-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="0ee3e-164">SignalR hub'ı için hizmet ve bitiş noktası ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="0ee3e-165">**BlazorSignalRApp.Server** projesinde *Startup.cs* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="0ee3e-166">`ChatHub` Sınıfın ad alanını dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="0ee3e-167">SignalR ve Yanıt Sıkıştırma Middleware hizmetleri `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="0ee3e-168">Denetleyiciler için uç noktalar `Startup.Configure` ile istemci tarafı geri dönüşleri arasında, hub için bir uç nokta ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="0ee3e-169">Sohbet için Razor bileşen kodu ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="0ee3e-170">**BlazorSignalRApp.Client** projesinde *Pages/Index.razor* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="0ee3e-171">Biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="0ee3e-172">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="0ee3e-172">Run the app</span></span>

1. <span data-ttu-id="0ee3e-173">Araçlama nız için kılavuzu izleyin:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ee3e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0ee3e-175">**Solution Explorer'da** **BlazorSignalRApp.Server** projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0ee3e-176">Hata ayıklama veya <kbd>Ctrl</kbd>+<kbd>F5</kbd> ile uygulamayı çalıştırmak için <kbd>F5</kbd> tuşuna basın ve uygulamayı hata ayıklamadan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ee3e-177">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ee3e-178">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0ee3e-179">Ad ve ileti her iki sayfada anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ee3e-181">Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ee3e-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0ee3e-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0ee3e-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0ee3e-183">VS Code, Sunucu uygulaması için bir başlatma profili oluşturmayı teklif ettiğinde `program` (*.vscode/launch.json*), giriş uygulamanın montajını işaret etmek için aşağıdakine benzer görünür (`{APPLICATION NAME}.Server.dll`):</span><span class="sxs-lookup"><span data-stu-id="0ee3e-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="0ee3e-184">Hata ayıklama veya <kbd>Ctrl</kbd>+<kbd>F5</kbd> ile uygulamayı çalıştırmak için <kbd>F5</kbd> tuşuna basın ve uygulamayı hata ayıklamadan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ee3e-185">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ee3e-186">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0ee3e-187">Ad ve ileti her iki sayfada anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-187">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ee3e-189">Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ee3e-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ee3e-190">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ee3e-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0ee3e-191">**Çözüm** kenar çubuğunda **BlazorSignalRApp.Server** projesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0ee3e-192">+Uygulamayı hata ayıklama<kbd>⌘</kbd>+yla çalıştırmak <kbd>için</kbd>+<kbd>↩</kbd>\*\* tuşuna <kbd>basın</kbd>veya uygulamayı hata ayıklamadan çalıştırmak için<kbd>↩.</kbd></span><span class="sxs-lookup"><span data-stu-id="0ee3e-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="0ee3e-193">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ee3e-194">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0ee3e-195">Ad ve ileti her iki sayfada anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-195">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ee3e-197">Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ee3e-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ee3e-198">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0ee3e-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0ee3e-199">Komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0ee3e-200">URL'yi adres çubuğundan kopyalayın, başka bir tarayıcı örneği veya sekmesini açın ve URL'yi adres çubuğuna yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0ee3e-201">Tarayıcıdan birini seçin, bir ad ve mesaj girin ve **Gönder** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0ee3e-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0ee3e-202">Ad ve ileti her iki sayfada anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-202">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly örnek uygulaması, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0ee3e-204">Tırnak: *Star Trek VI: Keşfedilmemiş Ülke* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0ee3e-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0ee3e-205">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="0ee3e-205">Next steps</span></span>

<span data-ttu-id="0ee3e-206">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ee3e-207">Blazor WebAssembly Barındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0ee3e-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0ee3e-208">İstemci SignalR kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="0ee3e-209">SignalR Hub ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="0ee3e-210">Hub için hizmet ve bitiş noktası ekleme SignalR SignalR</span><span class="sxs-lookup"><span data-stu-id="0ee3e-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0ee3e-211">Sohbet için Razor bileşen kodu ekleme</span><span class="sxs-lookup"><span data-stu-id="0ee3e-211">Add Razor component code for chat</span></span>

<span data-ttu-id="0ee3e-212">Uygulama oluşturma Blazor hakkında daha fazla Blazor bilgi edinmek için belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="0ee3e-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="0ee3e-213">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0ee3e-213">Additional resources</span></span>

* <xref:signalr/introduction>
