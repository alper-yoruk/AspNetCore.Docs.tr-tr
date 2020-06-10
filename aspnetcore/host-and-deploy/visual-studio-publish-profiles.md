---
title: ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)
author: rick-anderson
description: Visual Studio 'da yayımlama profilleri oluşturmayı ve bunları çeşitli hedeflere ASP.NET Core uygulama dağıtımlarını yönetmek için kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: f6fe5b4fc3eca82f2807bd0d2376bbf6ea3eb8dd
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106305"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="a3e63-103">ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)</span><span class="sxs-lookup"><span data-stu-id="a3e63-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="a3e63-104">[Sayed Ibrampahashve](https://github.com/sayedihashimi) [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="a3e63-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a3e63-105">Bu belge, yayımlama profillerinin oluşturulması ve kullanılması için Visual Studio 2019 veya sonraki bir sürümü kullanılarak odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="a3e63-106">Visual Studio ile oluşturulan yayımlama profilleri MSBuild ve Visual Studio ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="a3e63-107">Azure 'da yayımlama yönergeleri için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="a3e63-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="a3e63-108">`dotnet new mvc`Komut aşağıdaki kök düzeyi [ \<Project> öğeyi](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="a3e63-109">Önceki `<Project>` öğenin özniteliği, `Sdk` sırasıyla *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.props* ve *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.targets*öğesinden MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="a3e63-110">İçin varsayılan konum `$(MSBuildSDKsPath)` (Visual Studio 2019 Enterprise ile) *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\enterprise\msbuild\sdk* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="a3e63-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="a3e63-111">`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) ve `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)) dahil diğer SDK 'lara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="a3e63-112">Her bağımlı SDK ile ilişkili MSBuild özellikleri ve hedefleri içeri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="a3e63-113">Yayımlama hedefleri, kullanılan Yayımla yöntemine göre uygun hedef kümesini içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="a3e63-114">MSBuild veya Visual Studio bir projeyi yüklediğinde, aşağıdaki üst düzey eylemler gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="a3e63-115">Projeyi oluştur</span><span class="sxs-lookup"><span data-stu-id="a3e63-115">Build project</span></span>
* <span data-ttu-id="a3e63-116">Yayımlanacak işlem dosyaları</span><span class="sxs-lookup"><span data-stu-id="a3e63-116">Compute files to publish</span></span>
* <span data-ttu-id="a3e63-117">Dosyaları hedefe Yayımla</span><span class="sxs-lookup"><span data-stu-id="a3e63-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="a3e63-118">İşlem projesi öğeleri</span><span class="sxs-lookup"><span data-stu-id="a3e63-118">Compute project items</span></span>

<span data-ttu-id="a3e63-119">Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="a3e63-120">Öğe türü, dosyanın nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="a3e63-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="a3e63-121">Varsayılan olarak, *. cs* dosyaları `Compile` öğe listesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="a3e63-122">`Compile`Öğe listesindeki dosyalar derlenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="a3e63-123">`Content`Öğe listesi, derleme çıktılarına ek olarak yayımlanan dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="a3e63-124">Varsayılan olarak, ve desenleriyle eşleşen `wwwroot\**` dosyalar `**\*.config` `**\*.json` `Content` öğe listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="a3e63-125">Örneğin, `wwwroot\**` [Glob deseninin](https://gruntjs.com/configuring-tasks#globbing-patterns) *Wwwroot* klasörü ve alt klasörlerindeki tüm dosyalar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a3e63-126">[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a3e63-127">Sonuç olarak, desenlerle eşleşen dosyalar `**\*.cshtml` ve `**\*.razor` `Content` öğe listesine de dahildir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="a3e63-128">[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a3e63-129">Sonuç olarak, düzeniyle eşleşen dosyalar `**\*.cshtml` da `Content` öğe listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="a3e63-130">Yayınlama listesine açıkça bir dosya eklemek için, dosyayı, [dosyaları dahil et](#include-files) bölümünde gösterildiği gibi doğrudan *. csproj* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="a3e63-131">Visual Studio 'da veya komut satırından yayımlarken **Yayımla** düğmesini seçerken:</span><span class="sxs-lookup"><span data-stu-id="a3e63-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="a3e63-132">Özellikler/öğeler hesaplanır (oluşturmak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="a3e63-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="a3e63-133">**Yalnızca Visual Studio**: NuGet paketleri geri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="a3e63-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="a3e63-134">(Geri yüklemenin CLı üzerinde kullanıcı tarafından açık olması gerekir.)</span><span class="sxs-lookup"><span data-stu-id="a3e63-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="a3e63-135">Proje oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-135">The project builds.</span></span>
* <span data-ttu-id="a3e63-136">Yayımlama öğeleri hesaplanır (yayımlamak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="a3e63-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="a3e63-137">Proje yayımlandı (hesaplanan dosyalar yayımlama hedefine kopyalanır).</span><span class="sxs-lookup"><span data-stu-id="a3e63-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="a3e63-138">Proje dosyasına bir ASP.NET Core projesi başvurduğunda `Microsoft.NET.Sdk.Web` , Web uygulaması dizininin köküne bir *app_offline. htm* dosyası yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="a3e63-139">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="a3e63-140">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="a3e63-141">Temel komut satırı yayımlama</span><span class="sxs-lookup"><span data-stu-id="a3e63-141">Basic command-line publishing</span></span>

<span data-ttu-id="a3e63-142">Komut satırı yayımlama, .NET Core tarafından desteklenen tüm platformlarda çalışmaktadır ve Visual Studio 'Yu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="a3e63-143">Aşağıdaki örneklerde .NET Core CLI [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır ( *. csproj* dosyasını içerir).</span><span class="sxs-lookup"><span data-stu-id="a3e63-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="a3e63-144">Proje klasörü geçerli çalışma dizini değilse, proje dosyası yolunda açıkça geçiş yapın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="a3e63-145">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a3e63-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="a3e63-146">Bir Web uygulaması oluşturmak ve yayımlamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a3e63-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="a3e63-147">`dotnet publish`Komut aşağıdaki çıkışın bir çeşidini üretir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="a3e63-148">Varsayılan yayımlama klasörü biçimi \*bin\Debug \\ {Target Framework bilinen ad} \publish \\ \*şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="a3e63-149">Örneğin, \*Bin\debug\netcoreapp2,2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="a3e63-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="a3e63-150">Aşağıdaki komut bir `Release` derlemeyi ve yayımlama dizinini belirtir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="a3e63-151">`dotnet publish`Komutu, hedefi çağıran MSBuild 'i çağırır `Publish` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="a3e63-152">Öğesine geçirilen parametreler `dotnet publish` MSBuild 'e geçirilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="a3e63-153">`-c`Ve `-o` parametreleri `Configuration` sırasıyla MSBuild ve özellikler ile eşlenir `OutputPath` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="a3e63-154">MSBuild özellikleri aşağıdaki biçimlerden birini kullanarak geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="a3e63-155">Örneğin, aşağıdaki komut bir `Release` ağ paylaşımında bir derlemeyi yayımlar.</span><span class="sxs-lookup"><span data-stu-id="a3e63-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="a3e63-156">Ağ paylaşma, eğik çizgiler (/*saat*) ile belirtilir ve tüm .NET Core desteklenen platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="a3e63-157">Dağıtım için yayımlanan uygulamanın çalışmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="a3e63-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="a3e63-158">*Yayımla* klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="a3e63-159">Kilitli dosyalar kopyalanamadığından dağıtım gerçekleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="a3e63-160">Yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="a3e63-160">Publish profiles</span></span>

<span data-ttu-id="a3e63-161">Bu bölüm, bir yayımlama profili oluşturmak için Visual Studio 2019 veya üstünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="a3e63-162">Profil oluşturulduktan sonra, Visual Studio 'dan veya komut satırından yayımlama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="a3e63-163">Yayımlama profilleri Yayımlama sürecini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="a3e63-164">Aşağıdaki yollardan birini seçerek Visual Studio 'da bir yayımlama profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="a3e63-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="a3e63-165">**Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="a3e63-166">**Build** menüsünden **{Project Name} Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="a3e63-167">Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="a3e63-168">Projenin bir yayımlama profili yoksa, **bir yayımlama hedefi seçin** sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="a3e63-169">Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="a3e63-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a3e63-170">Azure App Service</span></span>
* <span data-ttu-id="a3e63-171">Linux üzerinde Azure App Service</span><span class="sxs-lookup"><span data-stu-id="a3e63-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="a3e63-172">Azure Sanal Makineler</span><span class="sxs-lookup"><span data-stu-id="a3e63-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="a3e63-173">Klasör</span><span class="sxs-lookup"><span data-stu-id="a3e63-173">Folder</span></span>
* <span data-ttu-id="a3e63-174">IIS, FTP, Web Dağıtımı (herhangi bir Web sunucusu için)</span><span class="sxs-lookup"><span data-stu-id="a3e63-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="a3e63-175">Profili içeri aktar</span><span class="sxs-lookup"><span data-stu-id="a3e63-175">Import Profile</span></span>

<span data-ttu-id="a3e63-176">En uygun yayımlama hedefini belirlemek için, [hangi yayımlama seçeneklerinin benim için](/visualstudio/ide/not-in-toc/web-publish-options)uygun olduğunu öğrenin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="a3e63-177">Hedef Yayımla **klasörü** seçildiğinde, yayımlanmış varlıkları depolamak için bir klasör yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="a3e63-178">Varsayılan klasör yolu, \*bin \\ {Project CONFIGURATION} \\ {Target Framework bilinen adı} \publish \\ \*şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="a3e63-179">Örneğin, \*Bin\release\netcoreapp2,2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="a3e63-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="a3e63-180">Tamamlanacak **Profil oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="a3e63-181">Bir yayımlama profili oluşturulduktan sonra, **Yayımla** sekmesinin içeriği değişir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="a3e63-182">Yeni oluşturulan profil bir açılan listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="a3e63-183">Aşağı açılan listenin altında **Yeni profil** oluştur ' u seçerek yeni bir profil oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a3e63-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="a3e63-184">Visual Studio 'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE Name}. pubxml* MSBuild dosyası oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="a3e63-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="a3e63-185">*. Pubxml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="a3e63-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="a3e63-186">Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="a3e63-187">Derleme ve yayımlama işlemini özelleştirmek için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="a3e63-188">Azure hedefine yayımlarken, *. pubxml* dosyası Azure abonelik tanımlarınızı içerir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="a3e63-189">Bu hedef türünde, bu dosyayı kaynak denetimine eklemek önerilmez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="a3e63-190">Azure olmayan bir hedefe yayımlarken, *. pubxml* dosyasını denetlemek güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="a3e63-191">Gizli bilgiler (yayımlama parolası gibi) Kullanıcı/makine düzeyinde şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="a3e63-192">*Özellikler/PublishProfiles/{PROFILE Name}. pubxml. User* dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="a3e63-193">Bu dosya hassas bilgileri depolayabildiğinden, kaynak denetimine denetlenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="a3e63-194">ASP.NET Core Web uygulamasının nasıl yayımlanacağı hakkında genel bir bakış için, bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="a3e63-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="a3e63-195">ASP.NET Core Web uygulaması yayımlamak için gereken MSBuild görevleri ve hedefleri, [ASPNET/WebSDK deposunda](https://github.com/aspnet/websdk)açık kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="a3e63-196">Aşağıdaki komutlar Folder, MSDeploy ve [kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="a3e63-197">MSDeploy platformlar arası destek olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="a3e63-198">**Klasör (platformlar arası):**</span><span class="sxs-lookup"><span data-stu-id="a3e63-198">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="a3e63-199">**MSDeploy**</span><span class="sxs-lookup"><span data-stu-id="a3e63-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="a3e63-200">**MSDeploy paketi:**</span><span class="sxs-lookup"><span data-stu-id="a3e63-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="a3e63-201">Yukarıdaki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="a3e63-201">In the preceding examples:</span></span>

* <span data-ttu-id="a3e63-202">`dotnet publish`ve `dotnet build` Azure 'da herhangi bir platformda yayımlanacak kudu API 'lerini destekler.</span><span class="sxs-lookup"><span data-stu-id="a3e63-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="a3e63-203">Visual Studio yayımlama, kudu API 'Lerini destekler, ancak Azure 'da platformlar arası yayımlama için WebSDK tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="a3e63-204">Komuta geçme `DeployOnBuild` `dotnet publish` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="a3e63-205">Daha fazla bilgi için bkz. [Microsoft. net. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="a3e63-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="a3e63-206">Projenin *Properties/PublishProfiles* klasörüne aşağıdaki içeriğe sahip bir yayımlama profili ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a3e63-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

## <a name="folder-publish-example"></a><span data-ttu-id="a3e63-207">Klasör Yayımla örneği</span><span class="sxs-lookup"><span data-stu-id="a3e63-207">Folder publish example</span></span>

<span data-ttu-id="a3e63-208">*Folderprofile*adlı bir profille yayımlarken, aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="a3e63-208">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="a3e63-209">.NET Core CLI [DotNet Build](/dotnet/core/tools/dotnet-build) komutu, `msbuild` derleme ve yayımlama işlemini çalıştırmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="a3e63-210">`dotnet build`Ve `msbuild` komutları, bir klasör profilinde geçirilerek eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="a3e63-211">`msbuild`Doğrudan Windows üzerinde çağrılırken, MSBuild 'in .NET Framework sürümü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="a3e63-212">`dotnet build`Klasör olmayan bir profilde çağırma:</span><span class="sxs-lookup"><span data-stu-id="a3e63-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="a3e63-213">`msbuild`MSDeploy kullanan çağırır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="a3e63-214">Hataya neden olur (Windows üzerinde çalışırken bile).</span><span class="sxs-lookup"><span data-stu-id="a3e63-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="a3e63-215">Klasör olmayan bir profille yayımlamak için `msbuild` doğrudan çağırın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="a3e63-216">Aşağıdaki klasör yayımlama profili, Visual Studio ile oluşturulmuştur ve bir ağ paylaşımında yayımlar:</span><span class="sxs-lookup"><span data-stu-id="a3e63-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="a3e63-217">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="a3e63-217">In the preceding example:</span></span>

* <span data-ttu-id="a3e63-218">`<ExcludeApp_Data>`Özelliği yalnızca BIR XML şeması gereksinimini karşılamak için vardır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="a3e63-219">`<ExcludeApp_Data>`Proje kökünde bir *App_Data* klasörü olsa bile, özelliğin yayımlama işlemi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="a3e63-220">*App_Data* klasörü, ASP.NET 4. x projelerinde olduğu gibi özel bir işleme almaz.</span><span class="sxs-lookup"><span data-stu-id="a3e63-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="a3e63-221">`<LastUsedBuildConfiguration>`Özelliği olarak ayarlanır `Release` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="a3e63-222">Visual Studio 'dan yayımlarken değeri, `<LastUsedBuildConfiguration>` Yayımlama işlemi başlatıldığında değeri kullanılarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="a3e63-223">`<LastUsedBuildConfiguration>`özeldir ve içeri aktarılan MSBuild dosyasında geçersiz kılınmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="a3e63-224">Ancak, bu özellik aşağıdaki yaklaşımlardan birini kullanarak komut satırından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="a3e63-225">.NET Core CLI kullanarak:</span><span class="sxs-lookup"><span data-stu-id="a3e63-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="a3e63-226">MSBuild 'i kullanma:</span><span class="sxs-lookup"><span data-stu-id="a3e63-226">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="a3e63-227">Daha fazla bilgi için bkz. [MSBuild: yapılandırma özelliğini ayarlama](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="a3e63-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="a3e63-228">Komut satırından bir MSDeploy uç noktasına yayımlama</span><span class="sxs-lookup"><span data-stu-id="a3e63-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="a3e63-229">Aşağıdaki örnek, *AzureWebApp*adlı Visual Studio tarafından oluşturulan bir ASP.NET Core Web uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="a3e63-230">Visual Studio ile bir Azure Apps yayımlama profili eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="a3e63-231">Profil oluşturma hakkında daha fazla bilgi için, [Yayımlama profilleri](#publish-profiles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="a3e63-232">Uygulamayı bir yayımlama profili kullanarak dağıtmak için, `msbuild` bir Visual Studio **Geliştirici komut istemi**komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="a3e63-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="a3e63-233">Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="a3e63-234">Daha kolay erişim için, Visual Studio 'daki **Araçlar** menüsüne komut istemi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a3e63-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="a3e63-235">Daha fazla bilgi için bkz. [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="a3e63-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="a3e63-236">MSBuild aşağıdaki komut sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="a3e63-236">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="a3e63-237">{PATH}: uygulamanın proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="a3e63-237">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="a3e63-238">{PROFILE}: yayımlama profilinin adı.</span><span class="sxs-lookup"><span data-stu-id="a3e63-238">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="a3e63-239">{USERNAME}: MSDeploy Kullanıcı adı.</span><span class="sxs-lookup"><span data-stu-id="a3e63-239">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="a3e63-240">{USERNAME}, yayımlama profilinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-240">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="a3e63-241">{PASSWORD}: MSDeploy parolası.</span><span class="sxs-lookup"><span data-stu-id="a3e63-241">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="a3e63-242">{PROFILE} öğesinden {PASSWORD} öğesini edinin *. PublishSettings* dosyası.</span><span class="sxs-lookup"><span data-stu-id="a3e63-242">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="a3e63-243">' Nı indirin *. PublishSettings* dosyası şunlardan biri:</span><span class="sxs-lookup"><span data-stu-id="a3e63-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="a3e63-244">**Çözüm Gezgini**: **View**  >  **bulut Gezginini**görüntüle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="a3e63-245">Azure aboneliğinize bağlanın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="a3e63-246">**Uygulama hizmetleri**'ni açın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-246">Open **App Services**.</span></span> <span data-ttu-id="a3e63-247">Uygulamaya sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-247">Right-click the app.</span></span> <span data-ttu-id="a3e63-248">**Yayımlama profilini indir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="a3e63-249">Azure portal: Web uygulamasının **genel bakış** panelinde **Yayımlama profilini al** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="a3e63-250">Aşağıdaki örnek, *AzureWebApp-Web dağıtımı*adlı bir yayımlama profili kullanır:</span><span class="sxs-lookup"><span data-stu-id="a3e63-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="a3e63-251">Bir yayımlama profili, Windows komut kabuğu 'ndan .NET Core CLI [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="a3e63-252">`dotnet msbuild`Komut, platformlar arası bir komuttur ve macOS ve Linux üzerinde ASP.NET Core uygulamalar derleyebilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="a3e63-253">Ancak, macOS ve Linux 'ta MSBuild, bir uygulamayı Azure 'a veya diğer MSDeploy uç noktalarına dağıtmıyor.</span><span class="sxs-lookup"><span data-stu-id="a3e63-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="a3e63-254">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="a3e63-254">Set the environment</span></span>

<span data-ttu-id="a3e63-255">`<EnvironmentName>`Uygulamanın [ortamını](xref:fundamentals/environments)ayarlamak için Publish profile (*. pubxml*) veya proje dosyasına özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a3e63-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="a3e63-256">*Web. config* dönüştürmelerine ihtiyaç duyuyorsanız (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak), bkz <xref:host-and-deploy/iis/transform-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="a3e63-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="a3e63-257">Dosyaları Dışla</span><span class="sxs-lookup"><span data-stu-id="a3e63-257">Exclude files</span></span>

<span data-ttu-id="a3e63-258">ASP.NET Core Web Apps yayımlandığında, aşağıdaki varlıklar dahil edilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="a3e63-259">Yapı yapıtları</span><span class="sxs-lookup"><span data-stu-id="a3e63-259">Build artifacts</span></span>
* <span data-ttu-id="a3e63-260">Aşağıdaki glob desenleriyle eşleşen klasörler ve dosyalar:</span><span class="sxs-lookup"><span data-stu-id="a3e63-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="a3e63-261">`**\*.config`(örneğin, *Web. config*)</span><span class="sxs-lookup"><span data-stu-id="a3e63-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="a3e63-262">`**\*.json`(örneğin, *appSettings. JSON*)</span><span class="sxs-lookup"><span data-stu-id="a3e63-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="a3e63-263">MSBuild, [Glob desenlerini](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler.</span><span class="sxs-lookup"><span data-stu-id="a3e63-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="a3e63-264">Örneğin, aşağıdaki öğe, `<Content>` metin (*. txt*) dosyalarının *wwwroot\content* klasörü ve alt klasörlerinde kopyalanmasını bastırır:</span><span class="sxs-lookup"><span data-stu-id="a3e63-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="a3e63-265">Önceki biçimlendirme bir yayımlama profiline veya *. csproj* dosyasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="a3e63-266">*. Csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="a3e63-267">Aşağıdaki `<MsDeploySkipRules>` öğe, tüm dosyaları *wwwroot\content* klasöründen dışlar:</span><span class="sxs-lookup"><span data-stu-id="a3e63-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="a3e63-268">`<MsDeploySkipRules>`dağıtım sitesinden *atlama* hedeflerini silmez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="a3e63-269">`<Content>`hedeflenen dosya ve klasörler dağıtım sitesinden silinir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="a3e63-270">Örneğin, dağıtılan bir Web uygulamasının aşağıdaki dosyalar olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="a3e63-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="a3e63-271">*Görünümler/Home/about1. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a3e63-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="a3e63-272">*Görünümler/Home/About2. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a3e63-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="a3e63-273">*Görünümler/Home/About3. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a3e63-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="a3e63-274">Aşağıdaki `<MsDeploySkipRules>` öğeler eklenirse, bu dosyalar dağıtım sitesinde silinmez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="a3e63-275">Önceki `<MsDeploySkipRules>` öğeler *Atlanan* dosyaların dağıtılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="a3e63-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="a3e63-276">Dağıtıldıktan sonra bu dosyaları silmez.</span><span class="sxs-lookup"><span data-stu-id="a3e63-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="a3e63-277">Aşağıdaki `<Content>` öğe, dağıtım sitesindeki hedeflenen dosyaları siler:</span><span class="sxs-lookup"><span data-stu-id="a3e63-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="a3e63-278">Önceki öğeyle komut satırı dağıtımı kullanmak `<Content>` , aşağıdaki çıkışın bir varyasyonunu verir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="a3e63-279">Dosyaları dahil etme</span><span class="sxs-lookup"><span data-stu-id="a3e63-279">Include files</span></span>

<span data-ttu-id="a3e63-280">Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="a3e63-281">[Genel dosya ekleme](#general-file-inclusion) bölümü, `DotNetPublishFiles` [Web SDK 'sında](xref:razor-pages/web-sdk)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="a3e63-282">[Seçmeli dosya ekleme](#selective-file-inclusion) bölümü, `ResolvedFileToPublish` [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="a3e63-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="a3e63-283">Web SDK .NET Core SDK bağlı olduğundan, her iki öğe bir ASP.NET Core projesinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="a3e63-284">Genel dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="a3e63-284">General file inclusion</span></span>

<span data-ttu-id="a3e63-285">Aşağıdaki örnek öğesi, `<ItemGroup>` proje dizininin dışında bulunan bir klasörü yayımlanmış sitenin bir klasörüne kopyalamayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="a3e63-286">Aşağıdaki biçimlendirmeye eklenen tüm dosyalar `<ItemGroup>` Varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="a3e63-287">Yukarıdaki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="a3e63-287">The preceding markup:</span></span>

* <span data-ttu-id="a3e63-288">*. Csproj* dosyasına veya yayımlama profiline eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="a3e63-289">*. Csproj* dosyasına eklenirse, bu, projedeki her bir yayımlama profiline eklenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="a3e63-290">`_CustomFiles`Özniteliğin glob düzeniyle eşleşen dosyaları depolamak için bir öğe bildirir `Include` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="a3e63-291">Düzende başvurulan *görüntüler* klasörü, proje dizininin dışında bulunur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="a3e63-292">Adlı [ayrılmış bir özellik](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), `$(MSBuildProjectDirectory)` Proje dosyasının mutlak yoluna çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="a3e63-293">Öğe için dosyaların bir listesini sağlar `DotNetPublishFiles` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="a3e63-294">Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="a3e63-295">Varsayılan değer, biçimlendirmede geçersiz kılınır ve gibi [iyi bilinen öğe meta verilerini](/visualstudio/msbuild/msbuild-well-known-item-metadata) kullanır `%(RecursiveDir)` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="a3e63-296">İç metin, yayımlanan sitenin *Wwwroot/görüntüler* klasörünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="a3e63-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="a3e63-297">Seçmeli dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="a3e63-297">Selective file inclusion</span></span>

<span data-ttu-id="a3e63-298">Aşağıdaki örnekte vurgulanan biçimlendirme şunları göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="a3e63-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="a3e63-299">Projenin dışında bulunan bir dosyayı yayınlanan sitenin *Wwwroot* klasörüne kopyalama.</span><span class="sxs-lookup"><span data-stu-id="a3e63-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="a3e63-300">*ReadMe2.MD* dosyasının adı korunur.</span><span class="sxs-lookup"><span data-stu-id="a3e63-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="a3e63-301">*Wwwroot\content* klasörü dışlanıyor.</span><span class="sxs-lookup"><span data-stu-id="a3e63-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="a3e63-302">*Views\home\about2,cshtml*hariç tutulanıyor.</span><span class="sxs-lookup"><span data-stu-id="a3e63-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="a3e63-303">Yukarıdaki örnek, `ResolvedFileToPublish` varsayılan davranışı özniteliğinde belirtilen dosyaları her zaman yayımlanan siteye kopyalamak için olan öğesini kullanır `Include` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="a3e63-304">`<CopyToPublishDirectory>`Ya da ' ın iç metniyle bir alt öğe ekleyerek varsayılan davranışı geçersiz kılın `Never` `PreserveNewest` .</span><span class="sxs-lookup"><span data-stu-id="a3e63-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="a3e63-305">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a3e63-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="a3e63-306">Daha fazla dağıtım örneği için bkz. [Web SDK deposu Benioku dosyası](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="a3e63-306">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="a3e63-307">Yayımlamadan önce veya sonra bir hedef Çalıştır</span><span class="sxs-lookup"><span data-stu-id="a3e63-307">Run a target before or after publishing</span></span>

<span data-ttu-id="a3e63-308">Yerleşik `BeforePublish` ve `AfterPublish` hedefler, yayımlama hedefinden önce veya sonra bir hedef yürütür.</span><span class="sxs-lookup"><span data-stu-id="a3e63-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="a3e63-309">Aşağıdaki öğeleri yayımlama profiline, yayımlamadan önce ve sonra da konsol iletilerini günlüğe kaydetmek için ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a3e63-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="a3e63-310">Güvenilmeyen bir sertifikayı kullanarak bir sunucuya yayımlama</span><span class="sxs-lookup"><span data-stu-id="a3e63-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="a3e63-311">`<AllowUntrustedCertificate>`Değerini yayımlama profiline değeri olan özelliği ekleyin `True` :</span><span class="sxs-lookup"><span data-stu-id="a3e63-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="a3e63-312">Kudu hizmeti</span><span class="sxs-lookup"><span data-stu-id="a3e63-312">The Kudu service</span></span>

<span data-ttu-id="a3e63-313">Azure App Service Web uygulaması dağıtımında dosyaları görüntülemek için [kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="a3e63-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="a3e63-314">`scm`Belirteci Web uygulaması adına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="a3e63-315">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a3e63-315">For example:</span></span>

| <span data-ttu-id="a3e63-316">URL</span><span class="sxs-lookup"><span data-stu-id="a3e63-316">URL</span></span>                                    | <span data-ttu-id="a3e63-317">Sonuç</span><span class="sxs-lookup"><span data-stu-id="a3e63-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="a3e63-318">Web App</span><span class="sxs-lookup"><span data-stu-id="a3e63-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="a3e63-319">Kudu hizmeti</span><span class="sxs-lookup"><span data-stu-id="a3e63-319">Kudu service</span></span> |

<span data-ttu-id="a3e63-320">Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [hata ayıklama konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a3e63-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3e63-321">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a3e63-321">Additional resources</span></span>

* <span data-ttu-id="a3e63-322">[Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), IIS sunucularına Web uygulamaları ve Web siteleri dağıtımını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="a3e63-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="a3e63-323">[Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): dağıtım için dosya sorunları ve istek özellikleri.</span><span class="sxs-lookup"><span data-stu-id="a3e63-323">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="a3e63-324">Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="a3e63-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
