---
title: ASP.NET Core kullanmaya başlayınBlazor
author: guardrex
description: Tercih ettiğiniz araç Blazor ile Blazor uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769461"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="2727d-103">ASP.NET Core Blazor kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="2727d-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="2727d-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="2727d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2727d-105">Blazor kullanmaya başlamak için araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="2727d-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2727d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2727d-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2727d-107">Blazor Server uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="2727d-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2727d-108">Blazor Server ve Blazor WebAssembly uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) ' in en son önizlemesini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="2727d-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="2727d-109">İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..</span><span class="sxs-lookup"><span data-stu-id="2727d-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2727d-110">Aşağıdaki komutu çalıştırarak Blazor WebAssembly önizleme şablonunu yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="2727d-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="2727d-111">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="2727d-111">Create a new project.</span></span>

1. <span data-ttu-id="2727d-112">**Blazor uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-112">Select **Blazor App**.</span></span> <span data-ttu-id="2727d-113">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-113">Select **Next**.</span></span>

1. <span data-ttu-id="2727d-114">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="2727d-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="2727d-115">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="2727d-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2727d-116">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-116">Select **Create**.</span></span>

1. <span data-ttu-id="2727d-117">Blazor WebAssembly deneyimi için (Visual Studio 16,6 Preview 2 veya üzeri), **Blazor Webassembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="2727d-118">Bir Blazor Server deneyimi için (Visual Studio 16,4 veya üzeri) **Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="2727d-119">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-119">Select **Create**.</span></span>

1. <span data-ttu-id="2727d-120">Uygulamayı çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="2727d-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2727d-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2727d-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2727d-122">[.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="2727d-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="2727d-123">İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükler:</span><span class="sxs-lookup"><span data-stu-id="2727d-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="2727d-124">İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..</span><span class="sxs-lookup"><span data-stu-id="2727d-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="2727d-125">3,2 Preview Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** .</span><span class="sxs-lookup"><span data-stu-id="2727d-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="2727d-126">Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2727d-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="2727d-127">[Visual Studio Code](https://code.visualstudio.com/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="2727d-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="2727d-128">En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' `debug.javascript.usePreview` a ayarlayın `true`.</span><span class="sxs-lookup"><span data-stu-id="2727d-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="2727d-129">Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2727d-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="2727d-130">Bir Blazor WebAssembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="2727d-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="2727d-131">Visual Studio Code 'de *WebApplication1* klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="2727d-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="2727d-132">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="2727d-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="2727d-133">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-133">Select **Yes**.</span></span>

1. <span data-ttu-id="2727d-134">Blazor sunucusu ile Visual Studio Code hata ayıklayıcıyı kullanarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2727d-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="2727d-135">Blazor WebAssembly ile, **.NET Core başlatma (Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın ve ardından Chrome başlatma yapılandırması **'Nda .NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir).</span><span class="sxs-lookup"><span data-stu-id="2727d-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="2727d-136">Daha fazla bilgi için bkz. <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="2727d-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="2727d-137">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="2727d-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2727d-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2727d-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2727d-139">Blazor sunucusu Mac için Visual Studio desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2727d-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="2727d-140">Blazor WebAssembly Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="2727d-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="2727d-141">MacOS üzerinde Blazor WebAssembly uygulamaları oluşturmak için **.NET Core CLI** sekmesindeki yönergeleri izleyin. İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..</span><span class="sxs-lookup"><span data-stu-id="2727d-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="2727d-142">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="2727d-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="2727d-143">**Dosya** > **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2727d-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="2727d-144">Kenar çubuğunda **.NET Core** > **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="2727d-145">**Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="2727d-146">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-146">Select **Next**.</span></span>

1. <span data-ttu-id="2727d-147">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="2727d-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="2727d-148">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="2727d-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="2727d-149">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="2727d-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="2727d-150">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-150">Select **Next**.</span></span>

1. <span data-ttu-id="2727d-151">**Proje adı** alanında, uygulamayı `WebApplication1`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2727d-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="2727d-152">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-152">Select **Create**.</span></span>

1. <span data-ttu-id="2727d-153">Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' **ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="2727d-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="2727d-154">Hata ayıklama Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="2727d-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="2727d-155">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="2727d-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="2727d-156">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2727d-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2727d-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2727d-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2727d-158">[.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="2727d-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="2727d-159">İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak Blazor WebAssembly önizleme şablonunu yükler:</span><span class="sxs-lookup"><span data-stu-id="2727d-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="2727d-160">İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..</span><span class="sxs-lookup"><span data-stu-id="2727d-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="2727d-161">3,2 Preview Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** .</span><span class="sxs-lookup"><span data-stu-id="2727d-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="2727d-162">Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2727d-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="2727d-163">Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:</span><span class="sxs-lookup"><span data-stu-id="2727d-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="2727d-164">Bir Blazor Weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="2727d-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="2727d-165">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="2727d-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="2727d-166">Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="2727d-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="2727d-167">Giriş</span><span class="sxs-lookup"><span data-stu-id="2727d-167">Home</span></span>
* <span data-ttu-id="2727d-168">Sayaç</span><span class="sxs-lookup"><span data-stu-id="2727d-168">Counter</span></span>
* <span data-ttu-id="2727d-169">Verileri getir</span><span class="sxs-lookup"><span data-stu-id="2727d-169">Fetch data</span></span>

<span data-ttu-id="2727d-170">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2727d-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2727d-171">Bir Web sayfasındaki sayacı normal şekilde artırma, JavaScript yazmayı gerektirir, ancak Blazor ile C# kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2727d-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="2727d-172">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="2727d-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="2727d-173">En üstteki `/counter` `@page` yönergeyle belirtilen şekilde tarayıcıda için bir istek, `Counter` bileşenin içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2727d-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="2727d-174">Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="2727d-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="2727d-175">**Bana tıklama** düğmesi her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="2727d-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="2727d-176">`onclick` Olay tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="2727d-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="2727d-177">`IncrementCount` Yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2727d-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="2727d-178">`currentCount` Artırılır.</span><span class="sxs-lookup"><span data-stu-id="2727d-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="2727d-179">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="2727d-179">The component is rendered again.</span></span>

<span data-ttu-id="2727d-180">Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.</span><span class="sxs-lookup"><span data-stu-id="2727d-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="2727d-181">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2727d-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="2727d-182">Örneğin, bileşene bir `Counter` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2727d-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="2727d-183">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="2727d-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="2727d-184">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2727d-184">Run the app.</span></span> <span data-ttu-id="2727d-185">Giriş sayfası, `Counter` bileşen tarafından sağlanmış kendi sayacıdır.</span><span class="sxs-lookup"><span data-stu-id="2727d-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="2727d-186">Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="2727d-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="2727d-187">`Counter` Bileşene bir parametre eklemek için bileşenin `@code` bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2727d-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="2727d-188">`[Parameter]` Özniteliği ile için `IncrementAmount` bir public özelliği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2727d-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="2727d-189">Değerini değerini `IncrementCount` `IncrementAmount` artırdığınızda kullanmak için yöntemini değiştirin `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="2727d-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="2727d-190">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="2727d-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="2727d-191">`IncrementAmount` Özniteliği kullanarak `Index` bileşenin `<Counter>` öğesinde öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2727d-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="2727d-192">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="2727d-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="2727d-193">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2727d-193">Run the app.</span></span> <span data-ttu-id="2727d-194">`Index` Bileşenin, **bana tıklama** düğmesi seçildiğinde her seferinde on ile artan kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="2727d-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2727d-195">' `Counter` De `/counter` bileşen (*Counter. Razor*), bir tane tarafından arttırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="2727d-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2727d-196">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="2727d-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="2727d-197">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2727d-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
