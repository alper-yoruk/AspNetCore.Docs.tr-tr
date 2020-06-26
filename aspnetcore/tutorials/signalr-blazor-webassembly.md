---
title: İle ASP.NET Core kullanma SignalRBlazor WebAssembly
author: guardrex
description: İle ASP.NET Core kullanan bir sohbet uygulaması oluşturun SignalR Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 5a58e7ae28842e2e8a0f3bae8f47e252839903fe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408883"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="de2bf-103">İle ASP.NET Core kullanma SignalRBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="de2bf-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="de2bf-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="de2bf-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="de2bf-105">Bu öğretici ile kullanarak gerçek zamanlı bir uygulama oluşturma hakkında temel bilgileri öğretir SignalR Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="de2bf-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="de2bf-106">Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="de2bf-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="de2bf-107">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="de2bf-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="de2bf-108">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="de2bf-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="de2bf-109">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="de2bf-110">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="de2bf-111">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="de2bf-111">Add Razor component code for chat</span></span>

<span data-ttu-id="de2bf-112">Bu öğreticinin sonunda, çalışan bir sohbet uygulamanız olacaktır.</span><span class="sxs-lookup"><span data-stu-id="de2bf-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="de2bf-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de2bf-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="de2bf-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="de2bf-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de2bf-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de2bf-116">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019 16,6 veya üzeri](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="de2bf-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="de2bf-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de2bf-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de2bf-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="de2bf-119">Mac için Visual Studio Sürüm 8,6 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="de2bf-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="de2bf-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de2bf-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="de2bf-121">Barındırılan Blazor WebAssembly uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="de2bf-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="de2bf-122">Araç seçiminiz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de2bf-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="de2bf-124">Visual Studio 16,6 veya üzeri ve .NET Core SDK 3.1.300 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="de2bf-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="de2bf-125">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="de2bf-125">Create a new project.</span></span>

1. <span data-ttu-id="de2bf-126">\*\* Blazor Uygulama\*\* ' yı seçin ve **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="de2bf-127">`BlazorSignalRApp` **Proje adı** alanına yazın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="de2bf-128">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="de2bf-129">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-129">Select **Create**.</span></span>

1. <span data-ttu-id="de2bf-130">\*\* Blazor WebAssembly Uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="de2bf-131">**Gelişmiş**' in altında, **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="de2bf-132">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de2bf-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de2bf-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="de2bf-134">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="de2bf-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="de2bf-135">Visual Studio Code, uygulamanın proje klasörünü açın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="de2bf-136">Uygulamayı derlemek ve hata ayıklamak için varlık Ekle iletişim kutusu göründüğünde **Evet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="de2bf-137">Visual Studio Code, klasörü otomatik olarak `.vscode` oluşturulan `launch.json` ve dosyaları ekler `tasks.json` .</span><span class="sxs-lookup"><span data-stu-id="de2bf-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de2bf-138">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="de2bf-139">En son [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/) sürümünü yükleyip aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="de2bf-140">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="de2bf-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="de2bf-141">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="de2bf-142">\*\* Blazor WebAssembly Uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="de2bf-143">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-143">Select **Next**.</span></span>

   <span data-ttu-id="de2bf-144">Aşağıdaki konfigürasyonları onaylayın:</span><span class="sxs-lookup"><span data-stu-id="de2bf-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="de2bf-145">**Hedef Framework** , **.NET Core 3,1**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="de2bf-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="de2bf-146">**Kimlik doğrulaması** **yok**olarak ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="de2bf-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="de2bf-147">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="de2bf-148">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-148">Select **Next**.</span></span>

1. <span data-ttu-id="de2bf-149">**Proje adı** alanında, uygulamayı adlandırın `BlazorSignalRApp` .</span><span class="sxs-lookup"><span data-stu-id="de2bf-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="de2bf-150">**Oluştur**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-150">Select **Create**.</span></span>

   <span data-ttu-id="de2bf-151">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="de2bf-152">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="de2bf-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="de2bf-153">Proje klasörüne gidip projenin çözüm dosyasını () açarak projeyi açın `.sln` .</span><span class="sxs-lookup"><span data-stu-id="de2bf-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de2bf-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de2bf-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="de2bf-155">Bir komut kabuğunda, aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="de2bf-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="de2bf-156">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="de2bf-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de2bf-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="de2bf-158">**Çözüm Gezgini**, projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="de2bf-159">**NuGet Paketlerini Yönet** iletişim kutusunda, **paket kaynağının** olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="de2bf-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="de2bf-160">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="de2bf-161">Arama sonuçlarında, [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) paketi seçin ve ardından **Install**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="de2bf-162">**Değişiklikleri Önizle** iletişim kutusu görüntülenirse **Tamam**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="de2bf-163">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul ediyorum** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de2bf-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de2bf-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="de2bf-165">**Tümleşik terminalde** (araç çubuğundan**Görünüm**  >  **terminali** ) aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="de2bf-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de2bf-166">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="de2bf-167">**Çözüm** kenar çubuğunda projeye sağ tıklayın `BlazorSignalRApp.Client` ve **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="de2bf-168">**NuGet Paketlerini Yönet** iletişim kutusunda, kaynak açılan kutusunun olarak ayarlandığını doğrulayın `nuget.org` .</span><span class="sxs-lookup"><span data-stu-id="de2bf-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="de2bf-169">**Araştır** seçiliyken, `Microsoft.AspNetCore.SignalR.Client` Arama kutusuna yazın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="de2bf-170">Arama sonuçlarında, paketin yanındaki onay kutusunu işaretleyin [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) ve **paket Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="de2bf-171">**Lisans kabulü** iletişim kutusu görüntülenirse, lisans şartlarını kabul ediyorsanız **kabul et** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de2bf-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de2bf-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="de2bf-173">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="de2bf-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="de2bf-174">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-174">Add a SignalR hub</span></span>

<span data-ttu-id="de2bf-175">`BlazorSignalRApp.Server`Projesinde, bir `Hubs` (plural) klasörü oluşturun ve aşağıdaki `ChatHub` sınıfı ( `Hubs/ChatHub.cs` ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="de2bf-176">Hub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="de2bf-177">`BlazorSignalRApp.Server`Projede `Startup.cs` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="de2bf-178">Sınıfın ad alanını `ChatHub` dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="de2bf-179">SignalRSıkıştırma ara yazılım hizmetlerini ekleme ve yanıtlama `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de2bf-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="de2bf-180">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="de2bf-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="de2bf-181">İşlem ardışık düzeninin yapılandırmasının en üstünde yanıt sıkıştırma ara yazılımı ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="de2bf-182">Denetleyiciler ve istemci tarafı geri dönüş uç noktaları arasında merkez için bir uç nokta ekleyin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="de2bf-183">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="de2bf-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="de2bf-184">`BlazorSignalRApp.Client`Projede `Pages/Index.razor` dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="de2bf-185">İşaretlemeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="de2bf-186">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="de2bf-186">Run the app</span></span>

1. <span data-ttu-id="de2bf-187">Araç kılavuzunuz için yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="de2bf-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de2bf-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="de2bf-189">**Çözüm Gezgini**, `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="de2bf-190">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="de2bf-191">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="de2bf-192">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="de2bf-193">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="de2bf-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="de2bf-194">![SignalRBlazor WebAssemblyörnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="de2bf-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="de2bf-195">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="de2bf-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="de2bf-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="de2bf-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="de2bf-197">VS Code, sunucu uygulaması () için bir başlatma profili oluşturmak üzere teklif edildiğinde `.vscode/launch.json` , `program` uygulamanın derlemesini () göstermek için giriş aşağıdakine benzer şekilde görünür `{APPLICATION NAME}.Server.dll` :</span><span class="sxs-lookup"><span data-stu-id="de2bf-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="de2bf-198">Uygulamayı hata ayıklama olmadan çalıştırmak için <kbd>F5</kbd> tuşuna basın veya uygulamayı hata ayıklamadan çalıştırmak için <kbd>CTRL</kbd> + <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="de2bf-199">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="de2bf-200">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="de2bf-201">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="de2bf-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="de2bf-202">![SignalRBlazor WebAssemblyörnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="de2bf-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="de2bf-203">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="de2bf-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="de2bf-204">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de2bf-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="de2bf-205">**Çözüm** kenar çubuğunda `BlazorSignalRApp.Server` projeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="de2bf-206">Uygulamayı <kbd>⌘</kbd>hata + <kbd>↩</kbd> <kbd>⌥</kbd> + <kbd>⌘</kbd> + ayıklamadan çalıştırmak için hata ayıklama veya ⌥ ⌘<kbd>↩</kbd> ile uygulamayı çalıştırmak için ⌘ ↩ tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="de2bf-207">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="de2bf-208">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="de2bf-209">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="de2bf-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="de2bf-210">![SignalRBlazor WebAssemblyörnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="de2bf-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="de2bf-211">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="de2bf-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de2bf-212">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="de2bf-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="de2bf-213">Komut kabuğu 'nda aşağıdaki komutları yürütün:</span><span class="sxs-lookup"><span data-stu-id="de2bf-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="de2bf-214">Adres çubuğundan URL 'yi kopyalayın, başka bir tarayıcı örneği veya sekme açın ve adres çubuğuna URL 'YI yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="de2bf-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="de2bf-215">Tarayıcı ' yı seçin, bir ad ve ileti girin ve iletiyi göndermek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="de2bf-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="de2bf-216">Ad ve ileti her iki sayfada da anında görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="de2bf-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="de2bf-217">![SignalRBlazor WebAssemblyörnek uygulama, değiştirilen iletileri gösteren iki tarayıcı penceresinde açılır.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="de2bf-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="de2bf-218">Alıntılar: *yıldız Trek VI: bulunan ülke* &copy; 1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="de2bf-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="de2bf-219">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="de2bf-219">Next steps</span></span>

<span data-ttu-id="de2bf-220">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="de2bf-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="de2bf-221">Blazor WebAssemblyBarındırılan uygulama projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="de2bf-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="de2bf-222">SignalRİstemci kitaplığını ekleme</span><span class="sxs-lookup"><span data-stu-id="de2bf-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="de2bf-223">Hub ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="de2bf-224">SignalRHub için hizmetler ve uç nokta ekleme SignalR</span><span class="sxs-lookup"><span data-stu-id="de2bf-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="de2bf-225">RazorSohbet için bileşen kodu ekle</span><span class="sxs-lookup"><span data-stu-id="de2bf-225">Add Razor component code for chat</span></span>

<span data-ttu-id="de2bf-226">Uygulama oluşturma hakkında daha fazla bilgi edinmek için Blazor Blazor belgelere bakın:</span><span class="sxs-lookup"><span data-stu-id="de2bf-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="de2bf-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="de2bf-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="de2bf-228">[SignalRkimlik doğrulaması için çıkış noktaları arası anlaşma](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="de2bf-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
