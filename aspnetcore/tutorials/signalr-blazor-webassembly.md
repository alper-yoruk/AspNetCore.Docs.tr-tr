---
title: SignalRBarındırılan bir uygulamayla ASP.NET Core kullanma Blazor WebAssembly
author: guardrex
description: İle ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 2f5630eac65b880bdefff2a4baf4f1878e981536
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058395"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="6121c-103">SignalRBarındırılan bir uygulamayla ASP.NET Core kullanma Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6121c-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="6121c-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="6121c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6121c-105">Bu öğretici ile kullanarak gerçek zamanlı bir uygulama oluşturma hakkında temel bilgileri öğretir SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="6121c-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="6121c-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="6121c-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6121c-107">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6121c-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6121c-108">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="6121c-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="6121c-109">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="6121c-110">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6121c-111">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="6121c-111">Add Razor component code for chat</span></span>

<span data-ttu-id="6121c-112">Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="6121c-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="6121c-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6121c-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6121c-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="6121c-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6121c-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,8 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6121c-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6121c-119">Mac için Visual Studio Sürüm 8,8 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6121c-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6121c-122">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,6 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="6121c-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-124">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6121c-125">Mac için Visual Studio Sürüm 8,6 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="6121c-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="6121c-127">Barındırılan Blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6121c-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="6121c-128">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="6121c-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6121c-130">Visual Studio 16,8 veya üzeri ve .NET Core SDK 5.0.0 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6121c-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="6121c-131">Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="6121c-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="6121c-132">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="6121c-132">Create a new project.</span></span>

1. <span data-ttu-id="6121c-133">**Blazor Uygulama** ' yı seçin ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="6121c-134">`BlazorSignalRApp` **Proje adı** alanına yazın.</span><span class="sxs-lookup"><span data-stu-id="6121c-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="6121c-135">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="6121c-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="6121c-136">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-136">Select **Create**.</span></span>

1. <span data-ttu-id="6121c-137">**Blazor WebAssembly Uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="6121c-138">**Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="6121c-139">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6121c-141">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="6121c-142">Visual Studio Code, uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="6121c-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="6121c-143">Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="6121c-144">Visual Studio Code, klasörü otomatik olarak `.vscode` oluşturulan `launch.json` ve dosyaları ekler `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="6121c-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-145">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6121c-146">En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="6121c-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="6121c-147">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6121c-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="6121c-148">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="6121c-149">**Blazor WebAssembly Uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="6121c-150">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-150">Select **Next**.</span></span>

1. <span data-ttu-id="6121c-151">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6121c-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="6121c-152">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="6121c-153">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-153">Select **Next**.</span></span>

1. <span data-ttu-id="6121c-154">**Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="6121c-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="6121c-155">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-155">Select **Create**.</span></span>

   <span data-ttu-id="6121c-156">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="6121c-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="6121c-157">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6121c-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="6121c-158">Proje klasörüne gidip projenin çözüm dosyasını () açarak projeyi açın `.sln` .</span><span class="sxs-lookup"><span data-stu-id="6121c-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6121c-160">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="6121c-161">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="6121c-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="6121c-163">**Çözüm Gezgini**, projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6121c-164">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6121c-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6121c-165">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="6121c-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6121c-166">Arama sonuçlarında, [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) paketi seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="6121c-167">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="6121c-168">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="6121c-170">**Tümleşik terminalde** (araç çubuğundan **Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-171">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6121c-172">**Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6121c-173">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6121c-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6121c-174">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="6121c-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="6121c-175">Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="6121c-176">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6121c-178">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="6121c-179">System. Text. kodlamalar. Web paketini ekleme</span><span class="sxs-lookup"><span data-stu-id="6121c-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="6121c-180">ASP.NET Core 3,1 uygulamasında 5.0.0 kullanılırken bir paket çözümleme sorunu nedeniyle [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) , `BlazorSignalRApp.Server` proje için bir paket başvurusu gerekir [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) .</span><span class="sxs-lookup"><span data-stu-id="6121c-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="6121c-181">Temel alınan sorun, .NET 5 ' in gelecekteki bir düzeltme eki sürümünde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="6121c-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="6121c-182">Daha fazla bilgi için bkz. [System.Text.Jshakkında, hiçbir bağımlılığı olmayan netcoreapp 3.0 'ı tanımlar (DotNet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span><span class="sxs-lookup"><span data-stu-id="6121c-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="6121c-183">[`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) `BlazorSignalRApp.Server` ASP.NET Core 3,1 barındırılan çözümünün projesine eklemek için Blazor , araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="6121c-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="6121c-185">**Çözüm Gezgini**, projeye sağ tıklayın `BlazorSignalRApp.Server` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6121c-186">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6121c-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6121c-187">**Araştır** seçiliyken, `System.Text.Encodings.Web` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="6121c-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="6121c-188">Arama sonuçlarında, [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) paketi seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="6121c-189">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="6121c-190">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="6121c-192">**Tümleşik terminalde** (araç çubuğundan **Görünüm** > **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-193">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6121c-194">**Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Server` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6121c-195">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="6121c-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="6121c-196">**Araştır** seçiliyken, `System.Text.Encodings.Web` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="6121c-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="6121c-197">Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="6121c-198">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6121c-200">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="6121c-201">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-201">Add a SignalR hub</span></span>

<span data-ttu-id="6121c-202">`BlazorSignalRApp.Server`Projesinde, bir `Hubs` (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı ( `Hubs/ChatHub.cs` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6121c-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="6121c-203">Hub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="6121c-204">`BlazorSignalRApp.Server`Projede `Startup.cs` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="6121c-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="6121c-205">Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6121c-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="6121c-206">SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6121c-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="6121c-207">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="6121c-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="6121c-208">İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6121c-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="6121c-209">Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6121c-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="6121c-210">SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6121c-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="6121c-211">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="6121c-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="6121c-212">İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6121c-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="6121c-213">Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6121c-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="6121c-214">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="6121c-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="6121c-215">`BlazorSignalRApp.Client`Projede `Pages/Index.razor` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="6121c-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="6121c-216">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6121c-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="6121c-217">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="6121c-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="6121c-218">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="6121c-218">Run the app</span></span>

<span data-ttu-id="6121c-219">Araç kılavuzunuz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="6121c-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6121c-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6121c-221">**Çözüm Gezgini**, `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6121c-222">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="6121c-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6121c-223">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6121c-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6121c-224">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6121c-225">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6121c-225">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6121c-227">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6121c-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6121c-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6121c-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6121c-229">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="6121c-229">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6121c-230">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6121c-230">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6121c-231">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-231">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6121c-232">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6121c-232">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6121c-234">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6121c-234">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6121c-235">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6121c-235">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6121c-236">**Çözüm** kenar çubuğunda `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-236">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="6121c-237">Uygulamayı <kbd></kbd>hata + <kbd></kbd> <kbd></kbd> + <kbd></kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="6121c-237">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="6121c-238">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6121c-238">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6121c-239">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-239">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6121c-240">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6121c-240">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6121c-242">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6121c-242">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6121c-243">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6121c-243">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="6121c-244">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="6121c-244">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="6121c-245">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="6121c-245">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6121c-246">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="6121c-246">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="6121c-247">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6121c-247">The name and message are displayed on both pages instantly:</span></span>

   ![::: No-Loc (SignalR):::::: No-Loc (Blazor WebAssembly)::: örnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="6121c-249">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="6121c-249">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="6121c-250">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6121c-250">Next steps</span></span>

<span data-ttu-id="6121c-251">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="6121c-251">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6121c-252">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="6121c-252">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="6121c-253">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="6121c-253">Add the SignalR client library</span></span>
> * <span data-ttu-id="6121c-254">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-254">Add a SignalR hub</span></span>
> * <span data-ttu-id="6121c-255">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="6121c-255">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="6121c-256">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="6121c-256">Add Razor component code for chat</span></span>

<span data-ttu-id="6121c-257">Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="6121c-257">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="6121c-258"><xref:blazor/index>
> [IdentitySunucu, WebSockets ve Server-Sent olayları ile taşıyıcı belirteci kimlik doğrulaması](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="6121c-258"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6121c-259">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6121c-259">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="6121c-260">SignalR kimlik doğrulaması için çıkış noktaları arası anlaşma</span><span class="sxs-lookup"><span data-stu-id="6121c-260">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
