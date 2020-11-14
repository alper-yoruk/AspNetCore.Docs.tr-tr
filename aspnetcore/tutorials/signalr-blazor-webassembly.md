---
title: 'İle ASP.NET Core kullanma SignalRBlazor WebAssembly'
author: guardrex
description: 'İle ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor WebAssembly .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: afbfc44db8cb833df7a79061f9bd73052859fec2
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508181"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="bd729-103">İle ASP.NET Core kullanma SignalRBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bd729-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="bd729-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="bd729-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd729-105">Bu öğretici ile kullanarak gerçek zamanlı bir uygulama oluşturma hakkında temel bilgileri öğretir SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="bd729-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="bd729-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="bd729-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd729-107">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd729-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="bd729-108">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="bd729-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="bd729-109">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="bd729-110">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="bd729-111">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="bd729-111">Add Razor component code for chat</span></span>

<span data-ttu-id="bd729-112">Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="bd729-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="bd729-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd729-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd729-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="bd729-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="bd729-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd729-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,8 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="bd729-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd729-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd729-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd729-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="bd729-119">Mac için Visual Studio Sürüm 8,8 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bd729-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="bd729-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd729-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="bd729-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd729-122">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,6 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="bd729-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd729-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd729-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd729-124">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="bd729-125">Mac için Visual Studio Sürüm 8,6 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="bd729-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="bd729-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd729-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="bd729-127">Barındırılan Blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd729-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="bd729-128">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="bd729-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd729-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="bd729-130">Visual Studio 16,8 veya üzeri ve .NET Core SDK 5.0.0 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="bd729-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="bd729-131">Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="bd729-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="bd729-132">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="bd729-132">Create a new project.</span></span>

1. <span data-ttu-id="bd729-133">**Blazor Uygulama** ' yı seçin ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="bd729-134">`BlazorSignalRApp` **Proje adı** alanına yazın.</span><span class="sxs-lookup"><span data-stu-id="bd729-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="bd729-135">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="bd729-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="bd729-136">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-136">Select **Create**.</span></span>

1. <span data-ttu-id="bd729-137">**Blazor WebAssembly Uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="bd729-138">**Gelişmiş** ' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="bd729-139">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd729-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd729-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bd729-141">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="bd729-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="bd729-142">Visual Studio Code, uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="bd729-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="bd729-143">Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="bd729-144">Visual Studio Code, klasörü otomatik olarak `.vscode` oluşturulan `launch.json` ve dosyaları ekler `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="bd729-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd729-145">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd729-146">En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="bd729-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="bd729-147">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bd729-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="bd729-148">Yan çubukta **Web ve konsol**  >  **uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="bd729-149">**Blazor WebAssembly Uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="bd729-150">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-150">Select **Next**.</span></span>

1. <span data-ttu-id="bd729-151">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bd729-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="bd729-152">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="bd729-153">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-153">Select **Next**.</span></span>

1. <span data-ttu-id="bd729-154">**Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="bd729-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="bd729-155">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-155">Select **Create**.</span></span>

   <span data-ttu-id="bd729-156">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="bd729-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="bd729-157">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="bd729-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="bd729-158">Proje klasörüne gidip projenin çözüm dosyasını () açarak projeyi açın `.sln` .</span><span class="sxs-lookup"><span data-stu-id="bd729-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bd729-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd729-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bd729-160">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="bd729-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="bd729-161">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="bd729-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd729-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="bd729-163">**Çözüm Gezgini** , projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-163">In **Solution Explorer** , right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="bd729-164">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="bd729-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="bd729-165">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="bd729-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="bd729-166">Arama sonuçlarında, [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) paketi seçin ve ardından **Install** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="bd729-167">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="bd729-168">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd729-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd729-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="bd729-170">**Tümleşik terminalde** (araç çubuğundan **Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="bd729-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd729-171">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd729-172">**Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="bd729-173">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="bd729-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="bd729-174">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="bd729-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="bd729-175">Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ve **paket Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="bd729-176">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bd729-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd729-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bd729-178">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="bd729-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="bd729-179">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-179">Add a SignalR hub</span></span>

<span data-ttu-id="bd729-180">`BlazorSignalRApp.Server`Projesinde, bir `Hubs` (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı ( `Hubs/ChatHub.cs` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bd729-180">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="bd729-181">Hub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-181">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="bd729-182">`BlazorSignalRApp.Server`Projede `Startup.cs` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="bd729-182">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="bd729-183">Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bd729-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="bd729-184">SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bd729-184">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="bd729-185">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="bd729-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="bd729-186">İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="bd729-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="bd729-187">Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bd729-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="bd729-188">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="bd729-188">Add Razor component code for chat</span></span>

1. <span data-ttu-id="bd729-189">`BlazorSignalRApp.Client`Projede `Pages/Index.razor` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="bd729-189">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="bd729-190">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="bd729-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="bd729-191">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="bd729-191">Run the app</span></span>

1. <span data-ttu-id="bd729-192">Araç kılavuzunuz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="bd729-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bd729-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="bd729-194">**Çözüm Gezgini** , `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-194">In **Solution Explorer** , select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="bd729-195">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="bd729-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="bd729-196">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bd729-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd729-197">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="bd729-198">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bd729-198">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd729-200">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd729-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="bd729-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bd729-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="bd729-202">VS Code, sunucu uygulaması () için bir başlatma profili oluşturmak üzere teklif edildiğinde `.vscode/launch.json` , `program` uygulamanın derlemesini () göstermek için giriş aşağıdakine benzer şekilde görünür `{APPLICATION NAME}.Server.dll` :</span><span class="sxs-lookup"><span data-stu-id="bd729-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="bd729-203">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="bd729-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="bd729-204">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bd729-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd729-205">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="bd729-206">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bd729-206">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd729-208">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd729-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bd729-209">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd729-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="bd729-210">**Çözüm** kenar çubuğunda `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-210">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="bd729-211">Uygulamayı <kbd>⌘</kbd>hata + <kbd>↩</kbd> <kbd>⌥</kbd> + <kbd>⌘</kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="bd729-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="bd729-212">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bd729-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd729-213">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="bd729-214">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bd729-214">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd729-216">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd729-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="bd729-217">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bd729-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="bd729-218">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="bd729-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="bd729-219">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bd729-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="bd729-220">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="bd729-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="bd729-221">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bd729-221">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="bd729-223">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="bd729-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="bd729-224">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="bd729-224">Next steps</span></span>

<span data-ttu-id="bd729-225">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="bd729-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd729-226">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd729-226">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="bd729-227">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="bd729-227">Add the SignalR client library</span></span>
> * <span data-ttu-id="bd729-228">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-228">Add a SignalR hub</span></span>
> * <span data-ttu-id="bd729-229">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="bd729-229">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="bd729-230">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="bd729-230">Add Razor component code for chat</span></span>

<span data-ttu-id="bd729-231">Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="bd729-231">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="bd729-232"><xref:blazor/index>
> [IdentitySunucu, WebSockets ve Server-Sent olayları ile taşıyıcı belirteci kimlik doğrulaması](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="bd729-232"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bd729-233">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bd729-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="bd729-234">SignalR kimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="bd729-234">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
