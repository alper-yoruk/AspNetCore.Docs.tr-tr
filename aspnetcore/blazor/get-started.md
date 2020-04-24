---
title: ASP.NET Core kullanmaya başlayınBlazor
author: guardrex
description: Tercih ettiğiniz araç Blazor ile Blazor uygulama oluşturarak başlayın.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111077"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="c3881-103">ASP.NET Core Blazor kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="c3881-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="c3881-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="c3881-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c3881-105">Blazor kullanmaya başlamak için araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="c3881-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3881-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3881-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3881-107">Blazor Server uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yüklemelisiniz.</span><span class="sxs-lookup"><span data-stu-id="c3881-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="c3881-108">Blazor Server ve Blazor WebAssembly uygulamaları oluşturmak için, **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) ' in en son önizlemesini yüklersiniz.</span><span class="sxs-lookup"><span data-stu-id="c3881-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="c3881-109">İki Blazor barındırma modeli hakkında daha fazla bilgi için, *Blazor WebAssembly* ve *Blazor Server*, <xref:blazor/hosting-models>bkz..</span><span class="sxs-lookup"><span data-stu-id="c3881-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c3881-110">Aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yüklemelisiniz:</span><span class="sxs-lookup"><span data-stu-id="c3881-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="c3881-111">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="c3881-111">Create a new project.</span></span>

1. <span data-ttu-id="c3881-112">**Blazor uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-112">Select **Blazor App**.</span></span> <span data-ttu-id="c3881-113">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-113">Select **Next**.</span></span>

1. <span data-ttu-id="c3881-114">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="c3881-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="c3881-115">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="c3881-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="c3881-116">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-116">Select **Create**.</span></span>

1. <span data-ttu-id="c3881-117">Blazor WebAssembly deneyimi için (Visual Studio 16,6 Preview 2 veya üzeri), **Blazor Webassembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="c3881-118">Bir Blazor Server deneyimi için (Visual Studio 16,4 veya üzeri) **Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="c3881-119">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-119">Select **Create**.</span></span>

1. <span data-ttu-id="c3881-120">Uygulamayı çalıştırmak için <kbd>CTRL</kbd>+<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="c3881-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c3881-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c3881-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c3881-122">[.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="c3881-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="c3881-123">İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükler:</span><span class="sxs-lookup"><span data-stu-id="c3881-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="c3881-124">3,2 Preview 4 Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** .</span><span class="sxs-lookup"><span data-stu-id="c3881-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="c3881-125">Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.</span><span class="sxs-lookup"><span data-stu-id="c3881-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="c3881-126">[Visual Studio Code](https://code.visualstudio.com/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="c3881-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="c3881-127">En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' `debug.javascript.usePreview` a ayarlayın `true`.</span><span class="sxs-lookup"><span data-stu-id="c3881-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="c3881-128">Bir Blazor sunucu deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="c3881-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="c3881-129">Bir Blazor WebAssembly deneyimi için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="c3881-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="c3881-130">İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>..</span><span class="sxs-lookup"><span data-stu-id="c3881-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c3881-131">Visual Studio Code 'de *WebApplication1* klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="c3881-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="c3881-132">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="c3881-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="c3881-133">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-133">Select **Yes**.</span></span>

1. <span data-ttu-id="c3881-134">Blazor sunucusu ile Visual Studio Code hata ayıklayıcıyı kullanarak uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c3881-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="c3881-135">Blazor WebAssembly ile, **.NET Core başlatma (Blazor tek başına)** başlatma yapılandırmasını kullanarak uygulamayı başlatın ve ardından Chrome başlatma yapılandırması **'Nda .NET Core hata ayıklama Blazor Web derlemesini** kullanarak tarayıcıyı başlatın (Chrome gerekir).</span><span class="sxs-lookup"><span data-stu-id="c3881-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="c3881-136">Daha fazla bilgi için bkz. <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="c3881-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="c3881-137">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="c3881-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c3881-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3881-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c3881-139">Blazor sunucusu Mac için Visual Studio desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c3881-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="c3881-140">Blazor WebAssembly Şu anda desteklenmiyor.</span><span class="sxs-lookup"><span data-stu-id="c3881-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="c3881-141">MacOS 'ta Blazor WebAssembly uygulamaları derlemek için **.NET Core CLI** sekmesindeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="c3881-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="c3881-142">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="c3881-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="c3881-143">**Dosya** > **yeni çözüm** ' ı seçin veya **Yeni bir proje**oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c3881-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="c3881-144">Kenar çubuğunda **.NET Core** > **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="c3881-145">**Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="c3881-146">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-146">Select **Create**.</span></span>

   <span data-ttu-id="c3881-147">Blazor sunucusu barındırma modeli hakkında daha fazla bilgi için bkz <xref:blazor/hosting-models>..</span><span class="sxs-lookup"><span data-stu-id="c3881-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c3881-148">**Hedef Framework 'ü** **.NET Core 3,1** olarak ayarlayın ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="c3881-149">**Proje adı** alanında, uygulamayı `WebApplication1`adlandırın.</span><span class="sxs-lookup"><span data-stu-id="c3881-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="c3881-150">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-150">Select **Create**.</span></span>

1. <span data-ttu-id="c3881-151">Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan** **Çalıştır ' ı seçin.** > </span><span class="sxs-lookup"><span data-stu-id="c3881-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="c3881-152">Uygulamayı hata *ayıklayıcıyla*çalıştırmak Için, **hata ayıklamayı Başlat** ile uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c3881-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="c3881-153">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="c3881-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c3881-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c3881-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c3881-155">[.NET Core 3,1 SDK 'sını](https://dotnet.microsoft.com/download/dotnet-core/3.1)yükler.</span><span class="sxs-lookup"><span data-stu-id="c3881-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="c3881-156">İsteğe bağlı olarak, aşağıdaki komutu çalıştırarak [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) önizleme şablonunu yükler:</span><span class="sxs-lookup"><span data-stu-id="c3881-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="c3881-157">3,2 Preview 4 Blazor WebAssembly şablonunu kullanmak için [.NET Core SDK Version 3.1.201 veya üzeri](https://dotnet.microsoft.com/download/dotnet-core/3.1) **gereklidir** .</span><span class="sxs-lookup"><span data-stu-id="c3881-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="c3881-158">Bir komut kabuğu 'nda çalıştırarak `dotnet --version` yüklü .NET Core SDK sürümünü onaylayın.</span><span class="sxs-lookup"><span data-stu-id="c3881-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="c3881-159">Bir Blazor sunucu deneyimi için aşağıdaki komutları bir komut kabuğunda yürütün:</span><span class="sxs-lookup"><span data-stu-id="c3881-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c3881-160">Bir Blazor Weelsembly deneyimi için komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="c3881-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="c3881-161">İki Blazor barındırma modeli, *Blazor Server* ve *Blazor webassembly*hakkında bilgi için bkz <xref:blazor/hosting-models>..</span><span class="sxs-lookup"><span data-stu-id="c3881-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="c3881-162">Bir tarayıcıda öğesine `https://localhost:5001`gidin.</span><span class="sxs-lookup"><span data-stu-id="c3881-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="c3881-163">Kenar çubuğu 'ndaki sekmelerde birden çok sayfa mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="c3881-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="c3881-164">Giriş</span><span class="sxs-lookup"><span data-stu-id="c3881-164">Home</span></span>
* <span data-ttu-id="c3881-165">Sayaç</span><span class="sxs-lookup"><span data-stu-id="c3881-165">Counter</span></span>
* <span data-ttu-id="c3881-166">Verileri getir</span><span class="sxs-lookup"><span data-stu-id="c3881-166">Fetch data</span></span>

<span data-ttu-id="c3881-167">Sayaç sayfasında, bir sayfa yenilemesi olmadan sayacı artırmak için **bana tıklama** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c3881-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="c3881-168">Bir Web sayfasındaki sayacı normal şekilde artırma, JavaScript yazmayı gerektirir, ancak Blazor ile C# kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c3881-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="c3881-169">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3881-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="c3881-170">En üstteki `/counter` `@page` yönergeyle belirtilen şekilde tarayıcıda için bir istek, `Counter` bileşenin içeriğini işlemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="c3881-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="c3881-171">Bileşenler, daha sonra, Kullanıcı arabirimini esnek ve verimli bir şekilde güncelleştirmek için kullanılabilen işleme ağacının bellek içi gösterimine işlenir.</span><span class="sxs-lookup"><span data-stu-id="c3881-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="c3881-172">**Bana tıklama** düğmesi her seçildiğinde:</span><span class="sxs-lookup"><span data-stu-id="c3881-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="c3881-173">`onclick` Olay tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="c3881-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="c3881-174">`IncrementCount` Yöntemi çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c3881-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="c3881-175">`currentCount` Artırılır.</span><span class="sxs-lookup"><span data-stu-id="c3881-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="c3881-176">Bileşen yeniden işlenir.</span><span class="sxs-lookup"><span data-stu-id="c3881-176">The component is rendered again.</span></span>

<span data-ttu-id="c3881-177">Çalışma zamanı, yeni içeriği önceki içerikle karşılaştırır ve yalnızca değiştirilen içeriği Belge Nesne Modeli (DOM) öğesine uygular.</span><span class="sxs-lookup"><span data-stu-id="c3881-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="c3881-178">HTML sözdizimini kullanarak başka bir bileşene bileşen ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c3881-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="c3881-179">Örneğin, bileşene bir `Counter` `<Counter />` öğe `Index` ekleyerek bileşeni uygulamanın giriş sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c3881-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="c3881-180">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3881-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="c3881-181">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c3881-181">Run the app.</span></span> <span data-ttu-id="c3881-182">Giriş sayfası, `Counter` bileşen tarafından sağlanmış kendi sayacıdır.</span><span class="sxs-lookup"><span data-stu-id="c3881-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="c3881-183">Bileşen parametreleri, alt bileşende özellikler ayarlamanıza olanak tanıyan öznitelikler veya [alt içerik](xref:blazor/components#child-content)kullanılarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c3881-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="c3881-184">`Counter` Bileşene bir parametre eklemek için bileşenin `@code` bloğunu güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c3881-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="c3881-185">`[Parameter]` Özniteliği ile için `IncrementAmount` bir public özelliği ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c3881-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="c3881-186">Değerini değerini `IncrementCount` `IncrementAmount` artırdığınızda kullanmak için yöntemini değiştirin `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="c3881-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="c3881-187">*Pages/Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3881-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="c3881-188">`IncrementAmount` Özniteliği kullanarak `Index` bileşenin `<Counter>` öğesinde öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="c3881-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="c3881-189">*Pages/Index. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c3881-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="c3881-190">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c3881-190">Run the app.</span></span> <span data-ttu-id="c3881-191">`Index` Bileşenin, **bana tıklama** düğmesi seçildiğinde her seferinde on ile artan kendi sayacı vardır.</span><span class="sxs-lookup"><span data-stu-id="c3881-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="c3881-192">' `Counter` De `/counter` bileşen (*Counter. Razor*), bir tane tarafından arttırmaya devam eder.</span><span class="sxs-lookup"><span data-stu-id="c3881-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c3881-193">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="c3881-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="c3881-194">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c3881-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
