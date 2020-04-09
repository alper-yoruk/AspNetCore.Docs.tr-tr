---
title: Visual Studio ASP.NET Core uygulama dağıtımı için profilleri (.pubxml) yayınlamak
author: rick-anderson
description: Visual Studio'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 274dd2cd528d3766aa07f69aac3470a131c79ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659378"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="38189-103">Visual Studio ASP.NET Core uygulama dağıtımı için profilleri (.pubxml) yayınlamak</span><span class="sxs-lookup"><span data-stu-id="38189-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="38189-104">Yazar: [Sayed Ibrahim Haşimi](https://github.com/sayedihashimi) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="38189-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="38189-105">Bu belge, yayın profilleri oluşturmak ve kullanmak için Visual Studio 2019 veya daha sonra sını kullanmaya odaklanır.</span><span class="sxs-lookup"><span data-stu-id="38189-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="38189-106">Visual Studio ile oluşturulan yayın profilleri MSBuild ve Visual Studio ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="38189-107">Azure'da yayımlama yla <xref:tutorials/publish-to-azure-webapp-using-vs>ilgili talimatlar için bkz.</span><span class="sxs-lookup"><span data-stu-id="38189-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="38189-108">Komut, `dotnet new mvc` aşağıdaki kök düzeyinde [ \<proje> öğesini](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:</span><span class="sxs-lookup"><span data-stu-id="38189-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="38189-109">`<Project>` Önceki `Sdk` öğenin özniteliği, sırasıyla *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* ve *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets'den*MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) alır.</span><span class="sxs-lookup"><span data-stu-id="38189-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="38189-110">(Visual Studio `$(MSBuildSDKsPath)` 2019 Enterprise ile) için varsayılan konum *%programdosyaları(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="38189-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="38189-111">`Microsoft.NET.Sdk.Web`(Web SDK) `Microsoft.NET.Sdk` (.NET Core SDK) ve `Microsoft.NET.Sdk.Razor` [(Razor SDK)](xref:razor-pages/sdk)dahil olmak üzere diğer SDK'lara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="38189-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="38189-112">MSBuild özellikleri ve her bağımlı SDK ile ilişkili hedefleri alınır.</span><span class="sxs-lookup"><span data-stu-id="38189-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="38189-113">Yayımlama hedefleri kullanılan yayımlama yöntemini temel alan uygun hedef kümesini içe aktarın.</span><span class="sxs-lookup"><span data-stu-id="38189-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="38189-114">MSBuild veya Visual Studio bir proje yükler, aşağıdaki üst düzey eylemler oluşur:</span><span class="sxs-lookup"><span data-stu-id="38189-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="38189-115">Proje oluşturma</span><span class="sxs-lookup"><span data-stu-id="38189-115">Build project</span></span>
* <span data-ttu-id="38189-116">Yayımlanacak dosyaları hesaplama</span><span class="sxs-lookup"><span data-stu-id="38189-116">Compute files to publish</span></span>
* <span data-ttu-id="38189-117">Dosyaları hedefe yayımlama</span><span class="sxs-lookup"><span data-stu-id="38189-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="38189-118">Proje öğelerini hesapla</span><span class="sxs-lookup"><span data-stu-id="38189-118">Compute project items</span></span>

<span data-ttu-id="38189-119">Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="38189-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="38189-120">Öğe türü, dosyanın nasıl işlenir olduğunu belirler.</span><span class="sxs-lookup"><span data-stu-id="38189-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="38189-121">Varsayılan olarak, *.cs* dosyaları öğe `Compile` listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="38189-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="38189-122">Madde listesindeki `Compile` dosyalar derlenir.</span><span class="sxs-lookup"><span data-stu-id="38189-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="38189-123">Madde `Content` listesi, yapı çıktılarına ek olarak yayımlanan dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="38189-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="38189-124">Varsayılan olarak, desenleri `wwwroot\**` `**\*.config`eşleşen `**\*.json` dosyalar , , `Content` , ve öğe listesine dahildir.</span><span class="sxs-lookup"><span data-stu-id="38189-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="38189-125">Örneğin, `wwwroot\**` [globbing deseni,](https://gruntjs.com/configuring-tasks#globbing-patterns) *wwwroot* klasöründeki tüm dosyalarla ve alt klasörlerine eşleşir.</span><span class="sxs-lookup"><span data-stu-id="38189-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="38189-126">Web SDK [Razor SDK](xref:razor-pages/sdk)ithal eder.</span><span class="sxs-lookup"><span data-stu-id="38189-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="38189-127">Sonuç olarak, desenlerle `**\*.cshtml` eşleşen `**\*.razor` dosyalar da madde `Content` listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="38189-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="38189-128">Web SDK [Razor SDK](xref:razor-pages/sdk)ithal eder.</span><span class="sxs-lookup"><span data-stu-id="38189-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="38189-129">Sonuç olarak, `**\*.cshtml` deseneşleşen dosyalar da öğe `Content` listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="38189-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="38189-130">Yayımlama listesine açıkça bir dosya eklemek için, dosyaları Dosyaları [Ekle](#include-files) bölümünde gösterildiği gibi doğrudan *.csproj* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="38189-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="38189-131">Visual Studio'da **Yayımla** düğmesini seçerken veya komut satırından yayımlarken:</span><span class="sxs-lookup"><span data-stu-id="38189-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="38189-132">Özellikler/öğeler (oluşturmak için gereken dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="38189-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="38189-133">**Sadece Visual Studio**: NuGet paketleri geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="38189-134">(Geri yükleme, CLI'de kullanıcı tarafından açık olmalıdır.)</span><span class="sxs-lookup"><span data-stu-id="38189-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="38189-135">Proje inşa eder.</span><span class="sxs-lookup"><span data-stu-id="38189-135">The project builds.</span></span>
* <span data-ttu-id="38189-136">Yayımlama öğeleri (yayımlamak için gerekli olan dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="38189-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="38189-137">Proje yayımlanır (hesaplanan dosyalar yayımlama hedefine kopyalanır).</span><span class="sxs-lookup"><span data-stu-id="38189-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="38189-138">Proje dosyasında bir `Microsoft.NET.Sdk.Web` ASP.NET Core proje başvuruları yapıldığında, web uygulama dizininin köküne bir *app_offline.htm* dosyası yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="38189-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="38189-139">Dosya bulunduğunda, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="38189-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="38189-140">Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="38189-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="38189-141">Temel komut satırı yayımlama</span><span class="sxs-lookup"><span data-stu-id="38189-141">Basic command-line publishing</span></span>

<span data-ttu-id="38189-142">Komut satırı yayımlama, .NET Çekirdek destekli tüm platformlarda çalışır ve Visual Studio gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="38189-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="38189-143">Aşağıdaki örneklerde,.NET Core CLI'nin [dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır *(.csproj* dosyasını içerir).</span><span class="sxs-lookup"><span data-stu-id="38189-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="38189-144">Proje klasörü geçerli çalışma dizini değilse, açıkça proje dosyası yoluna geçirin.</span><span class="sxs-lookup"><span data-stu-id="38189-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="38189-145">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="38189-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="38189-146">Bir web uygulaması oluşturmak ve yayınlamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="38189-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="38189-147">Komut `dotnet publish` aşağıdaki çıktının bir varyasyonunu üretir:</span><span class="sxs-lookup"><span data-stu-id="38189-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="38189-148">Varsayılan yayımlama klasörü biçimi *bin\Debug\\{TARGET FRAMEWORK\\MONIKER}\publish.*</span><span class="sxs-lookup"><span data-stu-id="38189-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="38189-149">Örneğin, *bin\Debug\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="38189-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="38189-150">Aşağıdaki komut, bir `Release` yapı yı ve yayımlama dizinini belirtir:</span><span class="sxs-lookup"><span data-stu-id="38189-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="38189-151">Komut, `dotnet publish` hedefi çağıran MSBuild'i `Publish` çağırır.</span><span class="sxs-lookup"><span data-stu-id="38189-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="38189-152">Geçirilen parametreler `dotnet publish` MSBuild'e geçirilir.</span><span class="sxs-lookup"><span data-stu-id="38189-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="38189-153">Ve `-c` `-o` parametreler, sırasıyla MSBuild'in `Configuration` ve `OutputPath` özelliklerinin eşlenir.</span><span class="sxs-lookup"><span data-stu-id="38189-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="38189-154">MSBuild özellikleri aşağıdaki biçimlerden biri kullanılarak geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="38189-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="38189-155">Örneğin, aşağıdaki komut bir `Release` ağ paylaşımıiçin bir yapı yayımlar.</span><span class="sxs-lookup"><span data-stu-id="38189-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="38189-156">Ağ payı,*(//r8/*) ileri eğik çizgilerle belirtilir ve tüm .NET Core destekli platformlarda çalışır.</span><span class="sxs-lookup"><span data-stu-id="38189-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="38189-157">Dağıtım için yayınlanan uygulamanın çalışmadığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="38189-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="38189-158">Uygulama çalışırken *yayımlama* klasöründeki dosyalar kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="38189-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="38189-159">Kilitli dosyalar kopyalanamadığından dağıtım gerçekleşemez.</span><span class="sxs-lookup"><span data-stu-id="38189-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="38189-160">Yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="38189-160">Publish profiles</span></span>

<span data-ttu-id="38189-161">Bu bölümde, bir yayımlama profili oluşturmak için Visual Studio 2019 veya daha sonra kullanır.</span><span class="sxs-lookup"><span data-stu-id="38189-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="38189-162">Profil oluşturulduktan sonra Visual Studio'dan veya komut satırından yayımlama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="38189-163">Yayımlama profilleri yayımlama işlemini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="38189-164">Aşağıdaki yollardan birini seçerek Visual Studio'da bir yayımlama profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="38189-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="38189-165">**Solution Explorer'da** projeyi sağ tıklatın ve **Yayımla'yı**seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="38189-166">**Yapı** menüsünden **"PROJE ADINI"** Yayımla'yı seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="38189-167">Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="38189-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="38189-168">Projede yayımlama profili yoksa, **yayımlama hedef** sayfasını seç görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="38189-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="38189-169">Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:</span><span class="sxs-lookup"><span data-stu-id="38189-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="38189-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="38189-170">Azure App Service</span></span>
* <span data-ttu-id="38189-171">Linux'ta Azure Uygulama Hizmeti</span><span class="sxs-lookup"><span data-stu-id="38189-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="38189-172">Azure Sanal Makineler</span><span class="sxs-lookup"><span data-stu-id="38189-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="38189-173">Klasör</span><span class="sxs-lookup"><span data-stu-id="38189-173">Folder</span></span>
* <span data-ttu-id="38189-174">IIS, FTP, Web Dağıtımı (herhangi bir web sunucusu için)</span><span class="sxs-lookup"><span data-stu-id="38189-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="38189-175">İthalat Profili</span><span class="sxs-lookup"><span data-stu-id="38189-175">Import Profile</span></span>

<span data-ttu-id="38189-176">En uygun yayımlama hedefini belirlemek için, hangi [yayımlama seçeneklerinin benim için doğru olduğunu](/visualstudio/ide/not-in-toc/web-publish-options)görün.</span><span class="sxs-lookup"><span data-stu-id="38189-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="38189-177">**Klasör** yayımlama hedefi seçildiğinde, yayımlanan varlıkları depolamak için bir klasör yolu belirleyin.</span><span class="sxs-lookup"><span data-stu-id="38189-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="38189-178">Varsayılan klasör yolu *\\bin {PROJECT\\CONFIGURATION} {TARGET FRAMEWORK MONIKER}\publish.\\*</span><span class="sxs-lookup"><span data-stu-id="38189-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="38189-179">Örneğin, *bin\Release\netcoreapp2.2\publish\\*.</span><span class="sxs-lookup"><span data-stu-id="38189-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="38189-180">Bitirmek için **Profil Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="38189-181">Yayımlama profili oluşturulduktan sonra **Yayımsekmesinin** içeriği değişir.</span><span class="sxs-lookup"><span data-stu-id="38189-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="38189-182">Yeni oluşturulan profil açılır listede görünür.</span><span class="sxs-lookup"><span data-stu-id="38189-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="38189-183">Açılan listenin altında, yeni bir profil oluşturmak için **yeni profil oluştur'u** seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="38189-184">Visual Studio'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="38189-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="38189-185">*.pubxml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="38189-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="38189-186">Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından tüketilir.</span><span class="sxs-lookup"><span data-stu-id="38189-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="38189-187">Yapı ve yayımlama işlemini özelleştirmek için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38189-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="38189-188">Bir Azure hedefine yayımlama *yaparken,.pubxml* dosyası Azure abonelik tanımlayıcınızı içerir.</span><span class="sxs-lookup"><span data-stu-id="38189-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="38189-189">Bu hedef türüyle, bu dosyayı kaynak denetimine ekleme önerilmez.</span><span class="sxs-lookup"><span data-stu-id="38189-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="38189-190">Azure olmayan bir hedefe yayımlama yaparken *,.pubxml* dosyasını iade etmek güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="38189-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="38189-191">Hassas bilgiler (yayımlama parolası gibi) kullanıcı/makine düzeyinde şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="38189-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="38189-192">*Özellikler/PublishProfiles/{PROFILE NAME}.pubxml.user* dosyasında saklanır.</span><span class="sxs-lookup"><span data-stu-id="38189-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="38189-193">Bu dosya hassas bilgileri depolayabildiği için kaynak denetiminde denetlenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="38189-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="38189-194">ASP.NET Core web uygulamasının nasıl yayımlandığına <xref:host-and-deploy/index>genel bir bakış için bkz.</span><span class="sxs-lookup"><span data-stu-id="38189-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="38189-195">ASP.NET Core web uygulaması yayınlamak için gereken MSBuild görevleri ve hedefleri [aspnet/websdk deposunda](https://github.com/aspnet/websdk)açık kaynak kodludur.</span><span class="sxs-lookup"><span data-stu-id="38189-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="38189-196">Aşağıdaki komutlar klasör, MSDeploy ve [Kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="38189-197">MSDeploy'ın platformlar arası desteği olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="38189-198">**Klasör (çapraz platform çalışır):**</span><span class="sxs-lookup"><span data-stu-id="38189-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="38189-199">**Msdeploy:**</span><span class="sxs-lookup"><span data-stu-id="38189-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="38189-200">**MSDeploy paketi:**</span><span class="sxs-lookup"><span data-stu-id="38189-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="38189-201">Önceki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="38189-201">In the preceding examples:</span></span>

* <span data-ttu-id="38189-202">`dotnet publish`ve `dotnet build` Kudu API'lerini herhangi bir platformdan Azure'da yayımlamak için destekleyin.</span><span class="sxs-lookup"><span data-stu-id="38189-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="38189-203">Visual Studio yayımlamak Kudu API'lerini destekler, ancak platformlar arası Azure'da yayımlanması için WebSDK tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="38189-204">Komutayı `DeployOnBuild` `dotnet publish` geçme.</span><span class="sxs-lookup"><span data-stu-id="38189-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="38189-205">Daha fazla bilgi için [Microsoft.NET.Sdk.Publish'e](https://github.com/aspnet/websdk#microsoftnetsdkpublish)bakın.</span><span class="sxs-lookup"><span data-stu-id="38189-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="38189-206">Projenin *Özellikler/Yayımlama Profilleri* klasörüne aşağıdaki içeriği içeren bir yayımlama profili ekleyin:</span><span class="sxs-lookup"><span data-stu-id="38189-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="38189-207">Klasör yayımlama örneği</span><span class="sxs-lookup"><span data-stu-id="38189-207">Folder publish example</span></span>

<span data-ttu-id="38189-208">*FolderProfile*adlı bir profille yayımlama yaparken aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="38189-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="38189-209">.NET Core CLI'nin [dotnet](/dotnet/core/tools/dotnet-build) `msbuild` build komutu, yapı ve yayımlama işlemini çalıştırmak için çağrıda bulunur.</span><span class="sxs-lookup"><span data-stu-id="38189-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="38189-210">Ve `dotnet build` `msbuild` komutları bir klasör profilinde geçerken eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="38189-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="38189-211">Doğrudan `msbuild` Windows'u ararken, MSBuild'in .NET Framework sürümü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38189-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="38189-212">Klasör `dotnet build` olmayan bir profili arama:</span><span class="sxs-lookup"><span data-stu-id="38189-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="38189-213">`msbuild`MSDeploy kullanan çağırır.</span><span class="sxs-lookup"><span data-stu-id="38189-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="38189-214">Başarısızlığa neden olan (Windows'ta çalışırken bile).</span><span class="sxs-lookup"><span data-stu-id="38189-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="38189-215">Klasör olmayan bir profille yayımlamak için doğrudan arayın. `msbuild`</span><span class="sxs-lookup"><span data-stu-id="38189-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="38189-216">Aşağıdaki klasör yayımlama profili Visual Studio ile oluşturuldu ve bir ağ paylaşımında yayımlanır:</span><span class="sxs-lookup"><span data-stu-id="38189-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="38189-217">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="38189-217">In the preceding example:</span></span>

* <span data-ttu-id="38189-218">Özellik `<ExcludeApp_Data>` yalnızca bir XML şema gereksinimini karşılamak için mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="38189-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="38189-219">`<ExcludeApp_Data>` Proje kökünde *App_Data* bir klasör olsa bile özelliğin yayımlama işlemi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="38189-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="38189-220">*App_Data* klasörü, ASP.NET 4.x projelerinde olduğu gibi özel muamele almaz.</span><span class="sxs-lookup"><span data-stu-id="38189-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="38189-221">`Release`Özellik. `<LastUsedBuildConfiguration>`</span><span class="sxs-lookup"><span data-stu-id="38189-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="38189-222">Visual Studio'dan yayımlanırken, yayımlama işlemi başlatıldığında değeri `<LastUsedBuildConfiguration>` değer kullanılarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="38189-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="38189-223">`<LastUsedBuildConfiguration>`özeldir ve içe aktarılan bir MSBuild dosyasında geçersiz kılınmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="38189-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="38189-224">Ancak bu özellik, aşağıdaki yaklaşımlardan biri kullanılarak komut satırından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="38189-225">.NET Çekirdek CLI'yi kullanma:</span><span class="sxs-lookup"><span data-stu-id="38189-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="38189-226">MSBuild kullanma:</span><span class="sxs-lookup"><span data-stu-id="38189-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="38189-227">Daha fazla bilgi için [MSBuild'e bakın: yapılandırma özelliği nasıl ayarlanan.](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)</span><span class="sxs-lookup"><span data-stu-id="38189-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="38189-228">Komut satırından MSDeploy bitiş noktasına yayımlama</span><span class="sxs-lookup"><span data-stu-id="38189-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="38189-229">Aşağıdaki örnekte Visual Studio tarafından oluşturulan *AzureWebApp*adlı ASP.NET Core web uygulaması kullanır.</span><span class="sxs-lookup"><span data-stu-id="38189-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="38189-230">Visual Studio ile Azure Apps yayımlama profili eklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="38189-231">Profil oluşturma hakkında daha fazla bilgi için [Profilleri Yayımla](#publish-profiles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="38189-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="38189-232">Bir yayımlama profili kullanarak uygulamayı `msbuild` dağıtmak için, visual studio **geliştirici komut komut u komut u komutunu**çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38189-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="38189-233">Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="38189-234">Daha kolay erişim için Visual Studio'daki **Araçlar** menüsüne komut istemini ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="38189-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="38189-235">Daha fazla bilgi için [Visual Studio için Geliştirici Komut Komut Ustem'e](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)bakın.</span><span class="sxs-lookup"><span data-stu-id="38189-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="38189-236">MSBuild aşağıdaki komut sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="38189-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="38189-237">{YOL} &ndash; Uygulamanın proje dosyasına giden yol.</span><span class="sxs-lookup"><span data-stu-id="38189-237">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="38189-238">{PROFIL} &ndash; Yayımlama profilinin adı.</span><span class="sxs-lookup"><span data-stu-id="38189-238">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="38189-239">{KULLANıCı Adı} &ndash; MSDeploy kullanıcı adı.</span><span class="sxs-lookup"><span data-stu-id="38189-239">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="38189-240">{USERNAME} yayımlama profilinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="38189-241">{PAROLA} &ndash; MSDeploy parolası.</span><span class="sxs-lookup"><span data-stu-id="38189-241">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="38189-242">{PROFILE}'dan {PASSWORD} *alın. PublishSettings* dosyası.</span><span class="sxs-lookup"><span data-stu-id="38189-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="38189-243">İndirin *. PublishSettings* dosyası aşağıdakilerden biri:</span><span class="sxs-lookup"><span data-stu-id="38189-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="38189-244">**Çözüm Gezgini**:**Bulut Gezginini** **Görüntüle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="38189-245">Azure aboneliğinizle bağlantı kurun.</span><span class="sxs-lookup"><span data-stu-id="38189-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="38189-246">**Uygulama Hizmetlerini**Açın.</span><span class="sxs-lookup"><span data-stu-id="38189-246">Open **App Services**.</span></span> <span data-ttu-id="38189-247">Uygulamaya sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="38189-247">Right-click the app.</span></span> <span data-ttu-id="38189-248">**İndir Yayın Profilini**Seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="38189-249">Azure portalı: Web uygulamasının **Genel Bakış** panelinde **yayımlama profilini al'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="38189-250">Aşağıdaki örnekte *AzureWebApp - Web Deploy*adlı bir yayımlama profili kullanır:</span><span class="sxs-lookup"><span data-stu-id="38189-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="38189-251">Bir yayımlama profili, Windows komut uyruktan .NET Core CLI'nin [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="38189-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="38189-252">Komut `dotnet msbuild` bir çapraz platform komutudur ve macOS ve Linux'ta ASP.NET Core uygulamaları derleyebilir.</span><span class="sxs-lookup"><span data-stu-id="38189-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="38189-253">Ancak, macOS ve Linux'taki MSBuild, bir uygulamayı Azure'a veya diğer MSDeploy bitiş noktalarına dağıtamaz.</span><span class="sxs-lookup"><span data-stu-id="38189-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="38189-254">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="38189-254">Set the environment</span></span>

<span data-ttu-id="38189-255">Uygulamanın `<EnvironmentName>` [ortamını](xref:fundamentals/environments)ayarlamak için özelliği yayımlama profiline (*.pubxml)* veya proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="38189-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="38189-256">*web.config* dönüşümleri (örneğin, yapılandırma, profil veya ortama göre ortam değişkenleri <xref:host-and-deploy/iis/transform-webconfig>ayarlama) gerekiyorsa, bkz.</span><span class="sxs-lookup"><span data-stu-id="38189-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="38189-257">Dosyaları hariç tut</span><span class="sxs-lookup"><span data-stu-id="38189-257">Exclude files</span></span>

<span data-ttu-id="38189-258">ASP.NET Core web uygulamalarını yayınlarken aşağıdaki varlıklar dahildir:</span><span class="sxs-lookup"><span data-stu-id="38189-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="38189-259">Yapı eserleri</span><span class="sxs-lookup"><span data-stu-id="38189-259">Build artifacts</span></span>
* <span data-ttu-id="38189-260">Aşağıdaki globbing desenleri eşleşen klasörler ve dosyalar:</span><span class="sxs-lookup"><span data-stu-id="38189-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="38189-261">`**\*.config`(örneğin, *web.config*)</span><span class="sxs-lookup"><span data-stu-id="38189-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="38189-262">`**\*.json`(örneğin, *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="38189-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="38189-263">MSBuild [globbing desenleri](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler.</span><span class="sxs-lookup"><span data-stu-id="38189-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="38189-264">Örneğin, aşağıdaki `<Content>` *öğe, wwwroot\content* klasöründe ve alt klasörlerinde metnin (*.txt*) dosyalarının kopyasını bastırır:</span><span class="sxs-lookup"><span data-stu-id="38189-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="38189-265">Önceki biçimlendirme bir yayımlama profiline veya *.csproj* dosyasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="38189-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="38189-266">*.csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="38189-267">Aşağıdaki `<MsDeploySkipRules>` *öğe, wwwroot\content* klasöründeki tüm dosyaları hariç tutar:</span><span class="sxs-lookup"><span data-stu-id="38189-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="38189-268">`<MsDeploySkipRules>`*atlama* hedeflerini dağıtım sitesinden silmez.</span><span class="sxs-lookup"><span data-stu-id="38189-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="38189-269">`<Content>`hedeflenen dosya ve klasörler dağıtım sitesinden silinir.</span><span class="sxs-lookup"><span data-stu-id="38189-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="38189-270">Örneğin, dağıtılan bir web uygulamasının aşağıdaki dosyalara sahip olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="38189-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="38189-271">*Görünümler/Ana Sayfa/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="38189-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="38189-272">*Görünümler/Ana Sayfa/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="38189-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="38189-273">*Görünümler/Ana Sayfa/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="38189-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="38189-274">Aşağıdaki `<MsDeploySkipRules>` öğeler eklenirse, bu dosyalar dağıtım sitesinde silinmez.</span><span class="sxs-lookup"><span data-stu-id="38189-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="38189-275">Önceki `<MsDeploySkipRules>` öğeler *atlanan* dosyaların dağıtılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="38189-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="38189-276">Dağıtıldıktan sonra bu dosyaları silmez.</span><span class="sxs-lookup"><span data-stu-id="38189-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="38189-277">Aşağıdaki `<Content>` öğe dağıtım sitesinde hedeflenen dosyaları siler:</span><span class="sxs-lookup"><span data-stu-id="38189-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="38189-278">Komut satırı dağıtımının önceki `<Content>` öğeyle birlikte kullanılması aşağıdaki çıktının bir varyasyonunu verir:</span><span class="sxs-lookup"><span data-stu-id="38189-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="38189-279">Dosyaları dahil etme</span><span class="sxs-lookup"><span data-stu-id="38189-279">Include files</span></span>

<span data-ttu-id="38189-280">Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar sıralanır.</span><span class="sxs-lookup"><span data-stu-id="38189-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="38189-281">[Genel dosya ekleme](#general-file-inclusion) bölümü, Web SDK'daki `DotNetPublishFiles` bir yayımlama hedefleri dosyası tarafından sağlanan öğeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="38189-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the Web SDK.</span></span> <span data-ttu-id="38189-282">[Seçici dosya ekleme](#selective-file-inclusion) bölümü, .NET Core SDK'daki bir yayımlama hedefleri dosyası tarafından sağlanan `ResolvedFileToPublish` öğeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="38189-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the .NET Core SDK.</span></span> <span data-ttu-id="38189-283">Web SDK .NET Core SDK'ya bağlı olduğundan, her iki öğe de ASP.NET Core projesinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="38189-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="38189-284">Genel dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="38189-284">General file inclusion</span></span>

<span data-ttu-id="38189-285">Aşağıdaki örnek `<ItemGroup>` öğesi, proje dizininin dışında bulunan bir klasörün yayımlanmış sitenin bir klasörüne kopyalanması gösterir.</span><span class="sxs-lookup"><span data-stu-id="38189-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="38189-286">Aşağıdaki biçimlendirmeye eklenen tüm `<ItemGroup>` dosyalar varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="38189-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="38189-287">Önceki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="38189-287">The preceding markup:</span></span>

* <span data-ttu-id="38189-288">*.csproj* dosyasına veya yayımlama profiline eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="38189-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="38189-289">*.csproj* dosyasına eklenirse, projedeki her yayımlama profiline eklenir.</span><span class="sxs-lookup"><span data-stu-id="38189-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="38189-290">Öznitelik `_CustomFiles` globbing deseni `Include` eşleşen dosyaları depolamak için bir öğe bildirir.</span><span class="sxs-lookup"><span data-stu-id="38189-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="38189-291">Desende başvurulan *görüntüler* klasörü proje dizininin dışında yer alır.</span><span class="sxs-lookup"><span data-stu-id="38189-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="38189-292">Ayrılmış [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)bir özellik `$(MSBuildProjectDirectory)`, adlı , proje dosyasının mutlak yoluna giderir.</span><span class="sxs-lookup"><span data-stu-id="38189-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="38189-293">`DotNetPublishFiles` Öğeye dosyaların bir listesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="38189-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="38189-294">Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur.</span><span class="sxs-lookup"><span data-stu-id="38189-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="38189-295">Varsayılan değer biçimlendirmede geçersiz kılınmış ve ' [.](/visualstudio/msbuild/msbuild-well-known-item-metadata) `%(RecursiveDir)`</span><span class="sxs-lookup"><span data-stu-id="38189-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="38189-296">İç metin, yayınlanan sitenin *wwwroot/images* klasörünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="38189-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="38189-297">Seçici dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="38189-297">Selective file inclusion</span></span>

<span data-ttu-id="38189-298">Aşağıdaki örnekte vurgulanan biçimlendirme şunları gösterir:</span><span class="sxs-lookup"><span data-stu-id="38189-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="38189-299">Projenin dışında bulunan bir dosyayı yayınlanan sitenin *wwwroot* klasörüne kopyalama.</span><span class="sxs-lookup"><span data-stu-id="38189-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="38189-300">*ReadMe2.md* dosya adı korunur.</span><span class="sxs-lookup"><span data-stu-id="38189-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="38189-301">*wwwroot\Content* klasörü hariç.</span><span class="sxs-lookup"><span data-stu-id="38189-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="38189-302">*Görünümler\Ana Sayfa\About2.cshtml*hariç .</span><span class="sxs-lookup"><span data-stu-id="38189-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="38189-303">Önceki örnek, varsayılan `ResolvedFileToPublish` davranışı her zaman yayımlanan siteye öznitelik `Include` sağlanan dosyaları kopyalamak için öğeyi kullanır.</span><span class="sxs-lookup"><span data-stu-id="38189-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="38189-304">Ya da `<CopyToPublishDirectory>` `Never` `PreserveNewest`iç metin ile bir alt öğe ekleyerek varsayılan davranışı geçersiz kılmak</span><span class="sxs-lookup"><span data-stu-id="38189-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="38189-305">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="38189-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="38189-306">Daha fazla dağıtım örneği için [Web SDK deposu Readme'a](https://github.com/aspnet/websdk)bakın.</span><span class="sxs-lookup"><span data-stu-id="38189-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="38189-307">Yayımlamadan önce veya sonra bir hedef çalıştırma</span><span class="sxs-lookup"><span data-stu-id="38189-307">Run a target before or after publishing</span></span>

<span data-ttu-id="38189-308">Yerleşik `BeforePublish` ve `AfterPublish` hedefler, yayımlama hedefinden önce veya sonra bir hedefi yürütür.</span><span class="sxs-lookup"><span data-stu-id="38189-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="38189-309">Yayımlamadan önce ve sonra konsol iletilerini günlüğe kaydetmek için yayımlama profiline aşağıdaki öğeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="38189-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="38189-310">Güvenilmeyen bir sertifika kullanarak sunucuda yayımlama</span><span class="sxs-lookup"><span data-stu-id="38189-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="38189-311">Yayımlama `<AllowUntrustedCertificate>` profiline değeri `True` olan özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="38189-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="38189-312">Kudu servisi</span><span class="sxs-lookup"><span data-stu-id="38189-312">The Kudu service</span></span>

<span data-ttu-id="38189-313">Azure App Service web uygulaması dağıtımındaki dosyaları görüntülemek için [Kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="38189-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="38189-314">Web uygulaması `scm` adının belirteci ekini ekle.</span><span class="sxs-lookup"><span data-stu-id="38189-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="38189-315">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="38189-315">For example:</span></span>

| <span data-ttu-id="38189-316">URL'si</span><span class="sxs-lookup"><span data-stu-id="38189-316">URL</span></span>                                    | <span data-ttu-id="38189-317">Sonuç</span><span class="sxs-lookup"><span data-stu-id="38189-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="38189-318">Web App</span><span class="sxs-lookup"><span data-stu-id="38189-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="38189-319">Kudu servisi</span><span class="sxs-lookup"><span data-stu-id="38189-319">Kudu service</span></span> |

<span data-ttu-id="38189-320">Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [Hata Ayıklama Konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38189-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38189-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38189-321">Additional resources</span></span>

* <span data-ttu-id="38189-322">[Web Dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), web uygulamalarının ve web sitelerinin IIS sunucularına dağıtımını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="38189-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="38189-323">[Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): Dosya sorunları ve dağıtım özellikleri isteği.</span><span class="sxs-lookup"><span data-stu-id="38189-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="38189-324">Visual Studio'dan azure vm'de ASP.NET bir Web Uygulaması yayınlama</span><span class="sxs-lookup"><span data-stu-id="38189-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
