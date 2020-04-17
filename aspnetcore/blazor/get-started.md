---
title: ASP.NET Core ile başlayınBlazor
author: guardrex
description: Seçtiğiniz araç Blazor ile Blazor bir uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 7fe4fbb082f08d4f71684c836a826d8b6dd888f6
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488734"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="f339c-103">Core BlazorASP.NET ile başlayın</span><span class="sxs-lookup"><span data-stu-id="f339c-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="f339c-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f339c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f339c-105">Blazor'a başlamak için, araç lama seçiminiz için kılavuzu izleyin:</span><span class="sxs-lookup"><span data-stu-id="f339c-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f339c-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f339c-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f339c-107">Blazor Server uygulamaları oluşturmak için [Visual Studio 2019'un](https://visualstudio.microsoft.com/downloads/) en son sürümünü ASP.NET ve web geliştirme iş yüküyle birlikte **yükleyin.**</span><span class="sxs-lookup"><span data-stu-id="f339c-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f339c-108">Blazor Server ve Blazor WebAssembly uygulamaları oluşturmak için [Visual Studio 2019'un](https://visualstudio.microsoft.com/vs/preview/) en son önizlemesini ASP.NET ve web geliştirme iş yüküyle birlikte **yükleyin.**</span><span class="sxs-lookup"><span data-stu-id="f339c-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="f339c-109">İki Blazor barındırma modelleri hakkında bilgi için, *Blazor WebAssembly* ve *Blazor Server*, bkz. <xref:blazor/hosting-models></span><span class="sxs-lookup"><span data-stu-id="f339c-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f339c-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu aşağıdaki komutu çalıştırarak yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f339c-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

1. <span data-ttu-id="f339c-111">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="f339c-111">Create a new project.</span></span>

1. <span data-ttu-id="f339c-112">**Blazor Uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-112">Select **Blazor App**.</span></span> <span data-ttu-id="f339c-113">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-113">Select **Next**.</span></span>

1. <span data-ttu-id="f339c-114">**Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="f339c-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f339c-115">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f339c-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f339c-116">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-116">Select **Create**.</span></span>

1. <span data-ttu-id="f339c-117">Blazor WebAssembly deneyimi (Visual Studio 16.6 Preview 2 veya sonraki) için **Blazor WebAssembly Uygulaması** şablonu'nu seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f339c-118">Blazor Server deneyimi (Visual Studio 16.4 veya sonrası) için **Blazor Server App** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f339c-119">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-119">Select **Create**.</span></span>

1. <span data-ttu-id="f339c-120">Uygulamayı çalıştırmak için <kbd>Ctrl</kbd>+<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f339c-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f339c-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f339c-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f339c-122">[.NET Core 3.1 SDK'yı](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f339c-123">İsteğe bağlı olarak aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f339c-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="f339c-124">[.NET Core SDK sürüm 3.1.201 veya sonraki](https://dotnet.microsoft.com/download/dotnet-core/3.1) 3.2 Preview 4 Blazor WebAssembly şablonu kullanmak için **gereklidir.**</span><span class="sxs-lookup"><span data-stu-id="f339c-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="f339c-125">Yüklü .NET Core SDK sürümünü `dotnet --version` komut kabuğunda çalıştırarak onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f339c-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f339c-126">[Visual Studio Kodunu](https://code.visualstudio.com/)Yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="f339c-127">Visual Studio Code uzantısı için en son [C#'ı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript Hata Ayıklayıcı (Gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını `debug.javascript.usePreview` ' olarak `true`ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f339c-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="f339c-128">Blazor Server deneyimi için aşağıdaki komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f339c-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f339c-129">Blazor WebAssembly deneyimi için, komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f339c-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f339c-130">İki Blazor barındırma modelleri hakkında bilgi için, *Blazor* Server <xref:blazor/hosting-models>ve *Blazor WebAssembly*, bkz.</span><span class="sxs-lookup"><span data-stu-id="f339c-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f339c-131">Visual Studio Code'da *WebApplication1* klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="f339c-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f339c-132">IDE, projeyi oluşturmak ve hata ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="f339c-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f339c-133">**Evet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-133">Select **Yes**.</span></span>

1. <span data-ttu-id="f339c-134">Blazor Server ile uygulamayı Visual Studio Code hata ayıklayıcısını kullanarak çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f339c-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="f339c-135">Blazor WebAssembly ile uygulamayı **.NET Core Launch (Blazor Standalone)** başlatma yapılandırmasını kullanarak başlatın ve chrome başlatma **yapılandırmasındaki .NET Core Debug Blazor Web Assembly'i** kullanarak tarayıcıyı başlatın (Chrome gerektirir).</span><span class="sxs-lookup"><span data-stu-id="f339c-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="f339c-136">Daha fazla bilgi için bkz. <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="f339c-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="f339c-137">Tarayıcıda, `https://localhost:5001`''</span><span class="sxs-lookup"><span data-stu-id="f339c-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f339c-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f339c-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f339c-139">Blazor Server, Visual Studio for Mac'te desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f339c-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="f339c-140">Blazor WebAssembly şu anda desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f339c-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="f339c-141">MacOS'ta Blazor WebAssembly uygulamaları oluşturmak için **.NET Core CLI** sekmesindeki kılavuzu izleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="f339c-142">[Mac için Visual Studio'u](https://visualstudio.microsoft.com/vs/mac/)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f339c-143">**Dosya** > **Yeni Çözüm'ünü** seçin veya Yeni Bir **Proje**oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f339c-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="f339c-144">Kenar çubuğunda **.NET Core** > **App'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="f339c-145">**Blazor Server App** şablonu'nu seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="f339c-146">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-146">Select **Create**.</span></span>

   <span data-ttu-id="f339c-147">Blazor Server barındırma modeli hakkında <xref:blazor/hosting-models>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f339c-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f339c-148">Hedef **Çerçeveyi** **.NET Core 3.1** olarak ayarlayın ve **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="f339c-149">Proje **Adı** alanında, uygulamayı `WebApplication1`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="f339c-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f339c-150">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-150">Select **Create**.</span></span>

1. <span data-ttu-id="f339c-151">*Hata ayıklama olmadan*uygulamayı çalıştırmak için**Hata Ayıklama olmadan** **Çalıştır'ı** > seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f339c-152">Uygulamayı *hata ayıklama yla*çalıştırmak için Uygulamayı Başlat **Hata Ayıklama** ile çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f339c-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="f339c-153">Geliştirme sertifikasına güvenen bir istem görünüyorsa, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="f339c-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f339c-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f339c-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f339c-155">[.NET Core 3.1 SDK'yı](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="f339c-156">İsteğe bağlı olarak aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f339c-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview4.20210.8
   ```

   > [!NOTE]
   > <span data-ttu-id="f339c-157">[.NET Core SDK sürüm 3.1.201 veya sonraki](https://dotnet.microsoft.com/download/dotnet-core/3.1) 3.2 Preview 4 Blazor WebAssembly şablonu kullanmak için **gereklidir.**</span><span class="sxs-lookup"><span data-stu-id="f339c-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="f339c-158">Yüklü .NET Core SDK sürümünü `dotnet --version` komut kabuğunda çalıştırarak onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f339c-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="f339c-159">Blazor Server deneyimi için, komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f339c-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f339c-160">Blazor WebAssembly deneyimi için, komut kabuğunda aşağıdaki komutları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="f339c-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="f339c-161">İki Blazor barındırma modelleri hakkında bilgi için, *Blazor* Server <xref:blazor/hosting-models>ve *Blazor WebAssembly*, bkz.</span><span class="sxs-lookup"><span data-stu-id="f339c-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f339c-162">Tarayıcıda, `https://localhost:5001`''</span><span class="sxs-lookup"><span data-stu-id="f339c-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="f339c-163">Kenar çubuğundaki sekmelerden birden çok sayfa bulunur:</span><span class="sxs-lookup"><span data-stu-id="f339c-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="f339c-164">Ev</span><span class="sxs-lookup"><span data-stu-id="f339c-164">Home</span></span>
* <span data-ttu-id="f339c-165">Sayaç</span><span class="sxs-lookup"><span data-stu-id="f339c-165">Counter</span></span>
* <span data-ttu-id="f339c-166">Veri getir</span><span class="sxs-lookup"><span data-stu-id="f339c-166">Fetch data</span></span>

<span data-ttu-id="f339c-167">Sayaç sayfasında, sayfa yenilemeden sayacı niçin artıya doğru artıya tıklayın **düğmesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="f339c-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f339c-168">Bir web sayfasında ki sayacı niçin artıya basması Blazor normalde JavaScript yazmayı gerektirir, ancak c#'ı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f339c-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="f339c-169">*Sayfalar/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f339c-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="f339c-170">`/counter` Üstteki `@page` yönergede belirtildiği gibi tarayıcıdaki istek, bileşenin `Counter` içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="f339c-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="f339c-171">Bileşenler, kullanıcı arasını esnek ve verimli bir şekilde güncelleştirmek için kullanılabilecek render ağacının bellek içi gösterimine dönüşür.</span><span class="sxs-lookup"><span data-stu-id="f339c-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="f339c-172">**Beni Tıklat** düğmesi her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="f339c-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="f339c-173">Olay `onclick` ateşlendi.</span><span class="sxs-lookup"><span data-stu-id="f339c-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="f339c-174">Yöntem `IncrementCount` denir.</span><span class="sxs-lookup"><span data-stu-id="f339c-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="f339c-175">Bu `currentCount` artış.</span><span class="sxs-lookup"><span data-stu-id="f339c-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="f339c-176">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="f339c-176">The component is rendered again.</span></span>

<span data-ttu-id="f339c-177">Çalışma süresi, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesnesi Modeli'ne (DOM) uygular.</span><span class="sxs-lookup"><span data-stu-id="f339c-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="f339c-178">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="f339c-179">Örneğin, `Index` bileşene `Counter` bir `<Counter />` öğe ekleyerek bileşeni uygulamanın ana sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="f339c-180">*Sayfalar/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f339c-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="f339c-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f339c-181">Run the app.</span></span> <span data-ttu-id="f339c-182">Ana sayfanın `Counter` bileşen tarafından sağlanan kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="f339c-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="f339c-183">Bileşen parametreleri öznitelikleri veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir , hangi alt bileşeni özellikleri ayarlamak için izin verir.</span><span class="sxs-lookup"><span data-stu-id="f339c-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="f339c-184">Bileşene `Counter` bir parametre eklemek için bileşenin `@code` bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f339c-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="f339c-185">Bir `[Parameter]` öznitelik `IncrementAmount` ile bir kamu malı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f339c-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="f339c-186">Değerini `IncrementCount` `IncrementAmount` artırırken kullanmak için yöntemi `currentCount`değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f339c-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="f339c-187">*Sayfalar/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f339c-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="f339c-188">Bir `IncrementAmount` öznitelik `Index` kullanarak `<Counter>` bileşenin öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="f339c-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="f339c-189">*Sayfalar/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f339c-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="f339c-190">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f339c-190">Run the app.</span></span> <span data-ttu-id="f339c-191">Bileşenin `Index` kendi sayacı vardır ve **her tıkla düğmesi** seçildiğinde on artış sağlar.</span><span class="sxs-lookup"><span data-stu-id="f339c-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f339c-192">Bileşen `Counter` *(Counter.razor*) `/counter` bir artış devam ediyor.</span><span class="sxs-lookup"><span data-stu-id="f339c-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f339c-193">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="f339c-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="f339c-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f339c-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
