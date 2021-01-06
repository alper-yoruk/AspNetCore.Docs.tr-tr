---
title: ASP.NET Core için araç oluşturma Blazor
author: guardrex
description: Uygulama derlemek için kullanılabilen araç hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764243"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="43b64-103">ASP.NET Core için araç oluşturma Blazor</span><span class="sxs-lookup"><span data-stu-id="43b64-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="43b64-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="43b64-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="43b64-105">**ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ' in en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="43b64-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="43b64-106">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="43b64-106">Create a new project.</span></span>

1. <span data-ttu-id="43b64-107">**Blazor Uygulama** seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-107">Select **Blazor App**.</span></span> <span data-ttu-id="43b64-108">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-108">Select **Next**.</span></span>

1. <span data-ttu-id="43b64-109">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="43b64-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="43b64-110">**Konum** girişinin doğru olduğunu onaylayın veya proje için bir konum belirtin.</span><span class="sxs-lookup"><span data-stu-id="43b64-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="43b64-111">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-111">Select **Create**.</span></span>

1. <span data-ttu-id="43b64-112">Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="43b64-113">Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="43b64-114">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-114">Select **Create**.</span></span>

   <span data-ttu-id="43b64-115">Barındırılan bir Blazor WebAssembly deneyim için **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="43b64-116">İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="43b64-117"><kbd></kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="43b64-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="43b64-118">ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="43b64-119">[.NET Core SDK](https://dotnet.microsoft.com/download)en son sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="43b64-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="43b64-120">SDK 'Yı daha önce yüklediyseniz, bir komut kabuğu 'nda aşağıdaki komutu yürüterek yüklü sürümünüzü belirleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="43b64-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="43b64-121">En son [Visual Studio Code](https://code.visualstudio.com)sürümünü yükler.</span><span class="sxs-lookup"><span data-stu-id="43b64-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="43b64-122">[Visual Studio Code uzantısı için](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)en son C# ' i yükler.</span><span class="sxs-lookup"><span data-stu-id="43b64-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="43b64-123">Bir Blazor WebAssembly deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="43b64-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="43b64-124">Barındırılan bir Blazor WebAssembly deneyim için, barındırılan seçenek ( `-ho` veya `--hosted` ) seçeneğini komuta ekleyin:</span><span class="sxs-lookup"><span data-stu-id="43b64-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="43b64-125">Bir Blazor Server deneyim için komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="43b64-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="43b64-126">İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="43b64-127">`WebApplication1`Visual Studio Code klasörü açın.</span><span class="sxs-lookup"><span data-stu-id="43b64-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="43b64-128">IDE, projeyi derlemek ve hatalarını ayıklamak için varlık eklemenizi ister.</span><span class="sxs-lookup"><span data-stu-id="43b64-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="43b64-129">**Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-129">Select **Yes**.</span></span>

1. <span data-ttu-id="43b64-130"><kbd></kbd> + Uygulamayı çalıştırmak için CTRL<kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="43b64-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="43b64-131">Geliştirme sertifikasına güven</span><span class="sxs-lookup"><span data-stu-id="43b64-131">Trust a development certificate</span></span>

<span data-ttu-id="43b64-132">Linux 'ta sertifikaya güvenmenin merkezi bir yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="43b64-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="43b64-133">Genellikle aşağıdaki yaklaşımlardan biri benimsenmiştir:</span><span class="sxs-lookup"><span data-stu-id="43b64-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="43b64-134">Uygulamanın URL 'sini tarayıcının dışlama listesinde hariç tutun.</span><span class="sxs-lookup"><span data-stu-id="43b64-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="43b64-135">İçin otomatik olarak imzalanan tüm sertifikalara güvenin `localhost` .</span><span class="sxs-lookup"><span data-stu-id="43b64-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="43b64-136">Sertifikayı tarayıcıdaki güvenilen sertifikalar listesine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="43b64-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="43b64-137">Daha fazla bilgi için, tarayıcı üreticiniz ve Linux dağıtım tarafından sunulan kılavuza bakın.</span><span class="sxs-lookup"><span data-stu-id="43b64-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="43b64-138">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/)'i yükler.</span><span class="sxs-lookup"><span data-stu-id="43b64-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="43b64-139">**Dosya**  >  **yeni çözüm** ' ı seçin veya **Başlangıç penceresinden** **Yeni** bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="43b64-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="43b64-140">Yan çubukta **Web ve konsol**  >  **uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="43b64-141">Bir Blazor WebAssembly deneyim için **Blazor WebAssembly uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="43b64-142">Bir Blazor Server deneyim için **Blazor Server uygulama** şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="43b64-143">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-143">Select **Next**.</span></span>

   <span data-ttu-id="43b64-144">İki barındırma modeli hakkında daha fazla bilgi için Blazor *Blazor WebAssembly* (tek başına ve barındırılan) ve *Blazor Server* bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="43b64-145">**Kimlik** doğrulamasının **kimlik doğrulaması yok** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="43b64-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="43b64-146">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-146">Select **Next**.</span></span>

1. <span data-ttu-id="43b64-147">Barındırılan bir Blazor WebAssembly deneyim için **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="43b64-148">**Proje adı** alanında, uygulamayı adlandırın `WebApplication1` .</span><span class="sxs-lookup"><span data-stu-id="43b64-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="43b64-149">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-149">Select **Create**.</span></span>

1. <span data-ttu-id="43b64-150">  >  Uygulamayı hata *ayıklayıcı olmadan* çalıştırmak için **hata ayıklama olmadan Başlat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="43b64-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="43b64-151">Uygulamayı   >  *hata ayıklayıcıyla* çalıştırmak için uygulamayı **Başlat hata ayıklaması** veya Çalıştır (&#9654;) düğmesini çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="43b64-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="43b64-152">Geliştirme sertifikasına güvenmek için bir istem görünürse, sertifikaya güvenin ve devam edin.</span><span class="sxs-lookup"><span data-stu-id="43b64-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="43b64-153">Sertifikaya güvenmek için Kullanıcı ve anahtarlık parolaların olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="43b64-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="43b64-154">ASP.NET Core HTTPS geliştirme sertifikasını güvenme hakkında daha fazla bilgi için bkz <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="43b64-155">Platformlar arası geliştirme için Visual Studio Code kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="43b64-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="43b64-156">[Visual Studio Code](https://code.visualstudio.com/) , uygulama geliştirmek için kullanılabilen, açık kaynaklı, platformlar arası tümleşik geliştirme ORTAMıDıR (IDE) Blazor .</span><span class="sxs-lookup"><span data-stu-id="43b64-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="43b64-157">Visual Studio Code ile geliştirme için yeni bir uygulama oluşturmak üzere .NET CLı 'yi kullanın Blazor .</span><span class="sxs-lookup"><span data-stu-id="43b64-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="43b64-158">Daha fazla bilgi için [Bu makalenin Linux sürümüne](/aspnet/core/blazor/tooling?pivots=linux)bakın.</span><span class="sxs-lookup"><span data-stu-id="43b64-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="43b64-159">Blazor Şablon seçenekleri</span><span class="sxs-lookup"><span data-stu-id="43b64-159">Blazor template options</span></span>

<span data-ttu-id="43b64-160">BlazorFramework, iki barındırma modelinin her biri için yeni uygulamalar oluşturmaya yönelik şablonlar sağlar Blazor .</span><span class="sxs-lookup"><span data-stu-id="43b64-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="43b64-161">Şablonlar, Blazor geliştirme için seçtiğiniz araç Blazor (Visual Studio, Mac için Visual Studio, Visual Studio Code veya .net CLI) ne olursa olsun yeni projeler ve çözümler oluşturmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="43b64-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="43b64-162">Blazor WebAssembly Proje şablonu: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="43b64-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="43b64-163">Blazor Server Proje şablonu: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="43b64-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="43b64-164">Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..</span><span class="sxs-lookup"><span data-stu-id="43b64-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="43b64-165">Şablon seçenekleri, `-h` `--help` [`dotnet new`](/dotnet/core/tools/dotnet-new) komut kabuğu 'ndaki CLI komutuna yardım seçeneği (veya) geçirerek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="43b64-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
