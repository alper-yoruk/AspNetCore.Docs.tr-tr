---
title: ASP.NET Core için araç oluşturmaBlazor
author: guardrex
description: Uygulama derlemek için kullanılabilen araç hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 538257597ec5c6c705d8280a817c409debe22224
ms.sourcegitcommit: c6467f86c09b1f608b09d37d33c8c43de7ae8bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85946811"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="ea6b5-103">ASP.NET Core için araç oluşturmaBlazor</span><span class="sxs-lookup"><span data-stu-id="ea6b5-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ea6b5-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="ea6b5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="ea6b5-105">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="ea6b5-106">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-106">Create a new project.</span></span>

1. <span data-ttu-id="ea6b5-107">\*\* Blazor Uygulama\*\*seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-107">Select **Blazor App**.</span></span> <span data-ttu-id="ea6b5-108">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-108">Select **Next**.</span></span>

1. <span data-ttu-id="ea6b5-109">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ea6b5-110">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ea6b5-111">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-111">Select **Create**.</span></span>

1. <span data-ttu-id="ea6b5-112">Bir Blazor WebAssembly deneyim için \*\* Blazor WebAssembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ea6b5-113">Bir Blazor Server deneyim için \*\* Blazor Server uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ea6b5-114">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-114">Select **Create**.</span></span>

   <span data-ttu-id="ea6b5-115">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="ea6b5-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ea6b5-116"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="ea6b5-117">[.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-117">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="ea6b5-118">SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="ea6b5-118">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="ea6b5-119">En son [Visual Studio Code](https://code.visualstudio.com/)sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-119">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="ea6b5-120">En son [C# for Visual Studio Code uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) ve [JavaScript hata ayıklayıcısı (gecelik)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) uzantısını ' a `debug.javascript.usePreview` ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="ea6b5-120">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   <span data-ttu-id="ea6b5-121">`debug.javascript.usePreview` `true` Vs Code Kullanıcı arabirimini kullanmak üzere ayarlamak için, **Dosya**  >  **tercihleri**  >  **ayarları** ' nı açın ve arama yapın `debug javascript use preview` .</span><span class="sxs-lookup"><span data-stu-id="ea6b5-121">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="ea6b5-122">**Node.js ve Chrome için yeni bir önizleme Içindeki JavaScript hata ayıklayıcısını kullan**onay kutusunu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-122">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="ea6b5-123">Bir Blazor WebAssembly deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ea6b5-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="ea6b5-124">Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="ea6b5-124">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="ea6b5-125">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="ea6b5-125">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ea6b5-126">`WebApplication1`Visual Studio Code klasörü açın.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-126">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="ea6b5-127">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-127">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ea6b5-128">**Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-128">Select **Yes**.</span></span>

1. <span data-ttu-id="ea6b5-129"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-129">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="ea6b5-130">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-130">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="ea6b5-131">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-131">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ea6b5-132">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-132">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="ea6b5-133">Bir Blazor WebAssembly deneyim için \*\* Blazor WebAssembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-133">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ea6b5-134">Bir Blazor Server deneyim için \*\* Blazor Server uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-134">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ea6b5-135">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-135">Select **Next**.</span></span>

   <span data-ttu-id="ea6b5-136">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="ea6b5-136">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="ea6b5-137">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="ea6b5-137">Confirm the following configurations:</span></span>

   * <span data-ttu-id="ea6b5-138">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-138">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="ea6b5-139">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-139">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="ea6b5-140">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-140">Select **Next**.</span></span>

1. <span data-ttu-id="ea6b5-141">**Proje adı** alanında, uygulamayı adlandırın `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="ea6b5-141">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="ea6b5-142">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-142">Select **Create**.</span></span>

1. <span data-ttu-id="ea6b5-143">**Run**  >  Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-143">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="ea6b5-144">Uygulamayı **Run**  >  *hata ayıklayıcıyla*çalıştırmak için uygulamayı**Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-144">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="ea6b5-145">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-145">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ea6b5-146">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ea6b5-146">The user and keychain passwords are required to trust the certificate.</span></span>

::: zone-end
