---
title: ASP.NET Core için araç oluşturma Blazor
author: guardrex
description: Uygulama derlemek için kullanılabilen araç hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: d7e3743d12c235c20cc27f6a3263e2994a9e160a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625836"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="80f90-103">ASP.NET Core için araç oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="80f90-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="80f90-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="80f90-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="80f90-105">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="80f90-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="80f90-106">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="80f90-106">Create a new project.</span></span>

1. <span data-ttu-id="80f90-107">\*\* Blazor Uygulama\*\*seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-107">Select **Blazor App**.</span></span> <span data-ttu-id="80f90-108">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-108">Select **Next**.</span></span>

1. <span data-ttu-id="80f90-109">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="80f90-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="80f90-110">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="80f90-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="80f90-111">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-111">Select **Create**.</span></span>

1. <span data-ttu-id="80f90-112">Bir Blazor WebAssembly deneyim için \*\* Blazor WebAssembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="80f90-113">Bir Blazor Server deneyim için \*\* Blazor Server uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="80f90-114">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-114">Select **Create**.</span></span>

   <span data-ttu-id="80f90-115">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="80f90-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="80f90-116"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="80f90-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="80f90-117">ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..</span><span class="sxs-lookup"><span data-stu-id="80f90-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="80f90-118">[.NET Core 3,1 SDK 'sının](https://dotnet.microsoft.com/download/dotnet-core/3.1)en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="80f90-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="80f90-119">SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="80f90-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="80f90-120">En son [Visual Studio Code](https://code.visualstudio.com/)sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="80f90-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="80f90-121">[Visual Studio Code uzantısı için](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)en son C# ' i yükler.</span><span class="sxs-lookup"><span data-stu-id="80f90-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="80f90-122">Bir Blazor WebAssembly deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="80f90-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="80f90-123">Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="80f90-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="80f90-124">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="80f90-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="80f90-125">`WebApplication1`Visual Studio Code klasörü açın.</span><span class="sxs-lookup"><span data-stu-id="80f90-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="80f90-126">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="80f90-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="80f90-127">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-127">Select **Yes**.</span></span>

1. <span data-ttu-id="80f90-128"><kbd>Ctrl</kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="80f90-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="80f90-129">Geliştirme sertifikasına güven</span><span class="sxs-lookup"><span data-stu-id="80f90-129">Trust a development certificate</span></span>

<span data-ttu-id="80f90-130">Linux 'ta sertifikaya güvenmenin merkezi bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="80f90-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="80f90-131">Genellikle aşağıdaki yaklaşımlardan biri benimsenmiştir:</span><span class="sxs-lookup"><span data-stu-id="80f90-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="80f90-132">Uygulamanın URL 'sini tarayıcının dışlama listesinde hariç tutun.</span><span class="sxs-lookup"><span data-stu-id="80f90-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="80f90-133">İçin otomatik olarak imzalanan tüm sertifikalara güvenin `localhost` .</span><span class="sxs-lookup"><span data-stu-id="80f90-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="80f90-134">Sertifikayı tarayıcıdaki güvenilen sertifikalar listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="80f90-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="80f90-135">Daha fazla bilgi için, tarayıcı ve Linux dağıtım tarafından sunulan kılavuza bakın.</span><span class="sxs-lookup"><span data-stu-id="80f90-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="80f90-136">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="80f90-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="80f90-137">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="80f90-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="80f90-138">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="80f90-139">Bir Blazor WebAssembly deneyim için \*\* Blazor WebAssembly uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="80f90-140">Bir Blazor Server deneyim için \*\* Blazor Server uygulama\*\* şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="80f90-141">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-141">Select **Next**.</span></span>

   <span data-ttu-id="80f90-142">İki barındırma modeli hakkında daha fazla bilgi için, Blazor *Blazor WebAssembly* *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="80f90-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="80f90-143">**Kimlik** doğrulamasının **kimlik doğrulaması yok**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="80f90-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="80f90-144">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-144">Select **Next**.</span></span>

1. <span data-ttu-id="80f90-145">**Proje adı** alanında, uygulamayı adlandırın `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="80f90-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="80f90-146">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-146">Select **Create**.</span></span>

1. <span data-ttu-id="80f90-147">**Run**  >  Uygulamayı hata *ayıklayıcı olmadan*çalıştırmak için**hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="80f90-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="80f90-148">Uygulamayı **Run**  >  *hata ayıklayıcıyla*çalıştırmak için uygulamayı**Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="80f90-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="80f90-149">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="80f90-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="80f90-150">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="80f90-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="80f90-151">ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..</span><span class="sxs-lookup"><span data-stu-id="80f90-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
