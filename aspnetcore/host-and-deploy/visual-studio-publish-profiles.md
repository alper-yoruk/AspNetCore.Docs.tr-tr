---
<span data-ttu-id="f5c39-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-102">'Blazor'</span></span>
- <span data-ttu-id="f5c39-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-103">'Identity'</span></span>
- <span data-ttu-id="f5c39-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-105">'Razor'</span></span>
- <span data-ttu-id="f5c39-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="f5c39-107">ASP.NET Core uygulama dağıtımı için Visual Studio yayımlama profilleri (. pubxml)</span><span class="sxs-lookup"><span data-stu-id="f5c39-107">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="f5c39-108">[Sayed Ibrampahashve](https://github.com/sayedihashimi) [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="f5c39-108">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f5c39-109">Bu belge, yayımlama profillerinin oluşturulması ve kullanılması için Visual Studio 2019 veya sonraki bir sürümü kullanılarak odaklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-109">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="f5c39-110">Visual Studio ile oluşturulan yayımlama profilleri MSBuild ve Visual Studio ile birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-110">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="f5c39-111">Azure 'da yayımlama yönergeleri için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..</span><span class="sxs-lookup"><span data-stu-id="f5c39-111">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="f5c39-112">`dotnet new mvc`Komut aşağıdaki kök düzeyi [ \<Project> öğeyi](/visualstudio/msbuild/project-element-msbuild)içeren bir proje dosyası üretir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-112">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="f5c39-113">Önceki `<Project>` öğenin özniteliği, `Sdk` sırasıyla *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.props* ve *$ (msbuildsdkspath) \Microsoft.net.SDK.Web\Sdk\Sdk.targets*öğesinden MSBuild [özelliklerini](/visualstudio/msbuild/msbuild-properties) ve [hedeflerini](/visualstudio/msbuild/msbuild-targets) içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-113">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="f5c39-114">İçin varsayılan konum `$(MSBuildSDKsPath)` (Visual Studio 2019 Enterprise ile) *% ProgramFiles (x86)% \ Microsoft Visual Studio\2019\enterprise\msbuild\sdk* klasörüdür.</span><span class="sxs-lookup"><span data-stu-id="f5c39-114">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="f5c39-115">`Microsoft.NET.Sdk.Web`([Web SDK](xref:razor-pages/web-sdk)) `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) ve `Microsoft.NET.Sdk.Razor` ([ Razor SDK](xref:razor-pages/sdk)) dahil diğer SDK 'lara bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="f5c39-116">Her bağımlı SDK ile ilişkili MSBuild özellikleri ve hedefleri içeri aktarılır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-116">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="f5c39-117">Yayımlama hedefleri, kullanılan Yayımla yöntemine göre uygun hedef kümesini içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-117">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="f5c39-118">MSBuild veya Visual Studio bir projeyi yüklediğinde, aşağıdaki üst düzey eylemler gerçekleşir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-118">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="f5c39-119">Projeyi oluştur</span><span class="sxs-lookup"><span data-stu-id="f5c39-119">Build project</span></span>
* <span data-ttu-id="f5c39-120">Yayımlanacak işlem dosyaları</span><span class="sxs-lookup"><span data-stu-id="f5c39-120">Compute files to publish</span></span>
* <span data-ttu-id="f5c39-121">Dosyaları hedefe Yayımla</span><span class="sxs-lookup"><span data-stu-id="f5c39-121">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="f5c39-122">İşlem projesi öğeleri</span><span class="sxs-lookup"><span data-stu-id="f5c39-122">Compute project items</span></span>

<span data-ttu-id="f5c39-123">Proje yüklendiğinde, [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) (dosyalar) hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-123">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="f5c39-124">Öğe türü, dosyanın nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="f5c39-124">The item type determines how the file is processed.</span></span> <span data-ttu-id="f5c39-125">Varsayılan olarak, *. cs* dosyaları `Compile` öğe listesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-125">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="f5c39-126">`Compile`Öğe listesindeki dosyalar derlenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-126">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="f5c39-127">`Content`Öğe listesi, derleme çıktılarına ek olarak yayımlanan dosyaları içerir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-127">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="f5c39-128">Varsayılan olarak, ve desenleriyle eşleşen `wwwroot\**` dosyalar `**\*.config` `**\*.json` `Content` öğe listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-128">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="f5c39-129">Örneğin, `wwwroot\**` [Glob deseninin](https://gruntjs.com/configuring-tasks#globbing-patterns) *Wwwroot* klasörü ve alt klasörlerindeki tüm dosyalar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-129">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5c39-130">[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-130">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f5c39-131">Sonuç olarak, desenlerle eşleşen dosyalar `**\*.cshtml` ve `**\*.razor` `Content` öğe listesine de dahildir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-131">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="f5c39-132">[Web SDK](xref:razor-pages/web-sdk) [ Razor 'sı SDK 'yı](xref:razor-pages/sdk)içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-132">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="f5c39-133">Sonuç olarak, düzeniyle eşleşen dosyalar `**\*.cshtml` da `Content` öğe listesine dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-133">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="f5c39-134">Yayınlama listesine açıkça bir dosya eklemek için, dosyayı, [dosyaları dahil et](#include-files) bölümünde gösterildiği gibi doğrudan *. csproj* dosyasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-134">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="f5c39-135">Visual Studio 'da veya komut satırından yayımlarken **Yayımla** düğmesini seçerken:</span><span class="sxs-lookup"><span data-stu-id="f5c39-135">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="f5c39-136">Özellikler/öğeler hesaplanır (oluşturmak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="f5c39-136">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="f5c39-137">**Yalnızca Visual Studio**: NuGet paketleri geri yüklendi.</span><span class="sxs-lookup"><span data-stu-id="f5c39-137">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="f5c39-138">(Geri yüklemenin CLı üzerinde kullanıcı tarafından açık olması gerekir.)</span><span class="sxs-lookup"><span data-stu-id="f5c39-138">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="f5c39-139">Proje oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-139">The project builds.</span></span>
* <span data-ttu-id="f5c39-140">Yayımlama öğeleri hesaplanır (yayımlamak için gereken dosyalar).</span><span class="sxs-lookup"><span data-stu-id="f5c39-140">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="f5c39-141">Proje yayımlandı (hesaplanan dosyalar yayımlama hedefine kopyalanır).</span><span class="sxs-lookup"><span data-stu-id="f5c39-141">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="f5c39-142">Proje dosyasına bir ASP.NET Core projesi başvurduğunda `Microsoft.NET.Sdk.Web` , Web uygulaması dizininin köküne bir *app_offline. htm* dosyası yerleştirilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-142">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="f5c39-143">Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline. htm* dosyasına hizmet verir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-143">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="f5c39-144">Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-144">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="f5c39-145">Temel komut satırı yayımlama</span><span class="sxs-lookup"><span data-stu-id="f5c39-145">Basic command-line publishing</span></span>

<span data-ttu-id="f5c39-146">Komut satırı yayımlama, .NET Core tarafından desteklenen tüm platformlarda çalışmaktadır ve Visual Studio 'Yu gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-146">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="f5c39-147">Aşağıdaki örneklerde .NET Core CLI [DotNet Publish](/dotnet/core/tools/dotnet-publish) komutu proje dizininden çalıştırılır ( *. csproj* dosyasını içerir).</span><span class="sxs-lookup"><span data-stu-id="f5c39-147">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="f5c39-148">Proje klasörü geçerli çalışma dizini değilse, proje dosyası yolunda açıkça geçiş yapın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-148">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="f5c39-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-149">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="f5c39-150">Bir Web uygulaması oluşturmak ve yayımlamak için aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f5c39-150">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="f5c39-151">`dotnet publish`Komut aşağıdaki çıkışın bir çeşidini üretir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-151">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="f5c39-152">Varsayılan yayımlama klasörü biçimi \*bin\Debug \\ {Target Framework bilinen ad} \publish \\ \*şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-152">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="f5c39-153">Örneğin, \*Bin\debug\netcoreapp2,2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="f5c39-153">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="f5c39-154">Aşağıdaki komut bir `Release` derlemeyi ve yayımlama dizinini belirtir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-154">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="f5c39-155">`dotnet publish`Komutu, hedefi çağıran MSBuild 'i çağırır `Publish` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-155">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="f5c39-156">Öğesine geçirilen parametreler `dotnet publish` MSBuild 'e geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-156">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="f5c39-157">`-c`Ve `-o` parametreleri `Configuration` sırasıyla MSBuild ve özellikler ile eşlenir `OutputPath` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-157">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="f5c39-158">MSBuild özellikleri aşağıdaki biçimlerden birini kullanarak geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-158">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="f5c39-159">Örneğin, aşağıdaki komut bir `Release` ağ paylaşımında bir derlemeyi yayımlar.</span><span class="sxs-lookup"><span data-stu-id="f5c39-159">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="f5c39-160">Ağ paylaşma, eğik çizgiler (/*saat*) ile belirtilir ve tüm .NET Core desteklenen platformlarda çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-160">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="f5c39-161">Dağıtım için yayımlanan uygulamanın çalışmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="f5c39-161">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="f5c39-162">*Yayımla* klasöründeki dosyalar, uygulama çalışırken kilitlenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-162">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="f5c39-163">Kilitli dosyalar kopyalanamadığından dağıtım gerçekleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-163">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="f5c39-164">Yayımlama profilleri</span><span class="sxs-lookup"><span data-stu-id="f5c39-164">Publish profiles</span></span>

<span data-ttu-id="f5c39-165">Bu bölüm, bir yayımlama profili oluşturmak için Visual Studio 2019 veya üstünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-165">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="f5c39-166">Profil oluşturulduktan sonra, Visual Studio 'dan veya komut satırından yayımlama kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-166">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="f5c39-167">Yayımlama profilleri Yayımlama sürecini basitleştirebilir ve herhangi bir sayıda profil bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-167">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="f5c39-168">Aşağıdaki yollardan birini seçerek Visual Studio 'da bir yayımlama profili oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f5c39-168">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="f5c39-169">**Çözüm Gezgini** projeye sağ tıklayın ve **Yayımla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-169">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="f5c39-170">**Build** menüsünden **{Project Name} Yayımla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-170">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="f5c39-171">Uygulama özellikleri sayfasının **Yayımla** sekmesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-171">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="f5c39-172">Projenin bir yayımlama profili yoksa, **bir yayımlama hedefi seçin** sayfası görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-172">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="f5c39-173">Aşağıdaki yayımlama hedeflerinden birini seçmeniz istenir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-173">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="f5c39-174">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f5c39-174">Azure App Service</span></span>
* <span data-ttu-id="f5c39-175">Linux üzerinde Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f5c39-175">Azure App Service on Linux</span></span>
* <span data-ttu-id="f5c39-176">Azure Sanal Makineler</span><span class="sxs-lookup"><span data-stu-id="f5c39-176">Azure Virtual Machines</span></span>
* <span data-ttu-id="f5c39-177">Klasör</span><span class="sxs-lookup"><span data-stu-id="f5c39-177">Folder</span></span>
* <span data-ttu-id="f5c39-178">IIS, FTP, Web Dağıtımı (herhangi bir Web sunucusu için)</span><span class="sxs-lookup"><span data-stu-id="f5c39-178">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="f5c39-179">Profili içeri aktar</span><span class="sxs-lookup"><span data-stu-id="f5c39-179">Import Profile</span></span>

<span data-ttu-id="f5c39-180">En uygun yayımlama hedefini belirlemek için, [hangi yayımlama seçeneklerinin benim için](/visualstudio/ide/not-in-toc/web-publish-options)uygun olduğunu öğrenin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-180">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="f5c39-181">Hedef Yayımla **klasörü** seçildiğinde, yayımlanmış varlıkları depolamak için bir klasör yolu belirtin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-181">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="f5c39-182">Varsayılan klasör yolu, \*bin \\ {Project CONFIGURATION} \\ {Target Framework bilinen adı} \publish \\ \*şeklindedir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-182">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="f5c39-183">Örneğin, \*Bin\release\netcoreapp2,2\publish \\ \*.</span><span class="sxs-lookup"><span data-stu-id="f5c39-183">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="f5c39-184">Tamamlanacak **Profil oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-184">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="f5c39-185">Bir yayımlama profili oluşturulduktan sonra, **Yayımla** sekmesinin içeriği değişir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-185">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="f5c39-186">Yeni oluşturulan profil bir açılan listede görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-186">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="f5c39-187">Aşağı açılan listenin altında **Yeni profil** oluştur ' u seçerek yeni bir profil oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f5c39-187">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="f5c39-188">Visual Studio 'nun yayımlama aracı, yayımlama profilini açıklayan bir *Özellikler/PublishProfiles/{PROFILE Name}. pubxml* MSBuild dosyası oluşturuyor.</span><span class="sxs-lookup"><span data-stu-id="f5c39-188">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="f5c39-189">*. Pubxml* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f5c39-189">The *.pubxml* file:</span></span>

* <span data-ttu-id="f5c39-190">Yayımlama yapılandırma ayarlarını içerir ve yayımlama işlemi tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-190">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="f5c39-191">Derleme ve yayımlama işlemini özelleştirmek için değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-191">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="f5c39-192">Azure hedefine yayımlarken, *. pubxml* dosyası Azure abonelik tanımlarınızı içerir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-192">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="f5c39-193">Bu hedef türünde, bu dosyayı kaynak denetimine eklemek önerilmez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-193">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="f5c39-194">Azure olmayan bir hedefe yayımlarken, *. pubxml* dosyasını denetlemek güvenlidir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-194">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="f5c39-195">Gizli bilgiler (yayımlama parolası gibi) Kullanıcı/makine düzeyinde şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-195">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="f5c39-196">*Özellikler/PublishProfiles/{PROFILE Name}. pubxml. User* dosyasında depolanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-196">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="f5c39-197">Bu dosya hassas bilgileri depolayabildiğinden, kaynak denetimine denetlenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-197">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="f5c39-198">ASP.NET Core Web uygulamasının nasıl yayımlanacağı hakkında genel bir bakış için, bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="f5c39-198">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="f5c39-199">ASP.NET Core Web uygulaması yayımlamak için gereken MSBuild görevleri ve hedefleri, [ASPNET/WebSDK deposunda](https://github.com/aspnet/websdk)açık kaynaktır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-199">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="f5c39-200">Aşağıdaki komutlar Folder, MSDeploy ve [kudu](https://github.com/projectkudu/kudu/wiki) yayımlama profillerini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-200">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="f5c39-201">MSDeploy platformlar arası destek olmadığından, aşağıdaki MSDeploy seçenekleri yalnızca Windows 'da desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-201">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="f5c39-202">**Klasör (platformlar arası):**</span><span class="sxs-lookup"><span data-stu-id="f5c39-202">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="f5c39-203">**MSDeploy**</span><span class="sxs-lookup"><span data-stu-id="f5c39-203">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="f5c39-204">**MSDeploy paketi:**</span><span class="sxs-lookup"><span data-stu-id="f5c39-204">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="f5c39-205">Yukarıdaki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="f5c39-205">In the preceding examples:</span></span>

* <span data-ttu-id="f5c39-206">`dotnet publish`ve `dotnet build` Azure 'da herhangi bir platformda yayımlanacak kudu API 'lerini destekler.</span><span class="sxs-lookup"><span data-stu-id="f5c39-206">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="f5c39-207">Visual Studio yayımlama, kudu API 'Lerini destekler, ancak Azure 'da platformlar arası yayımlama için WebSDK tarafından desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-207">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="f5c39-208">Komuta geçme `DeployOnBuild` `dotnet publish` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-208">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="f5c39-209">Daha fazla bilgi için bkz. [Microsoft. net. SDK. Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="f5c39-209">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="f5c39-210">Projenin *Properties/PublishProfiles* klasörüne aşağıdaki içeriğe sahip bir yayımlama profili ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-210">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

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

## <a name="folder-publish-example"></a><span data-ttu-id="f5c39-211">Klasör Yayımla örneği</span><span class="sxs-lookup"><span data-stu-id="f5c39-211">Folder publish example</span></span>

<span data-ttu-id="f5c39-212">*Folderprofile*adlı bir profille yayımlarken, aşağıdaki komutlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="f5c39-212">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="f5c39-213">.NET Core CLI [DotNet Build](/dotnet/core/tools/dotnet-build) komutu, `msbuild` derleme ve yayımlama işlemini çalıştırmak için çağırır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-213">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="f5c39-214">`dotnet build`Ve `msbuild` komutları, bir klasör profilinde geçirilerek eşdeğerdir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-214">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="f5c39-215">`msbuild`Doğrudan Windows üzerinde çağrılırken, MSBuild 'in .NET Framework sürümü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-215">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="f5c39-216">`dotnet build`Klasör olmayan bir profilde çağırma:</span><span class="sxs-lookup"><span data-stu-id="f5c39-216">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="f5c39-217">`msbuild`MSDeploy kullanan çağırır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-217">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="f5c39-218">Hataya neden olur (Windows üzerinde çalışırken bile).</span><span class="sxs-lookup"><span data-stu-id="f5c39-218">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="f5c39-219">Klasör olmayan bir profille yayımlamak için `msbuild` doğrudan çağırın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-219">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="f5c39-220">Aşağıdaki klasör yayımlama profili, Visual Studio ile oluşturulmuştur ve bir ağ paylaşımında yayımlar:</span><span class="sxs-lookup"><span data-stu-id="f5c39-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="f5c39-221">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="f5c39-221">In the preceding example:</span></span>

* <span data-ttu-id="f5c39-222">`<ExcludeApp_Data>`Özelliği yalnızca BIR XML şeması gereksinimini karşılamak için vardır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-222">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="f5c39-223">`<ExcludeApp_Data>`Proje kökünde bir *App_Data* klasörü olsa bile, özelliğin yayımlama işlemi üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-223">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="f5c39-224">*App_Data* klasörü, ASP.NET 4. x projelerinde olduğu gibi özel bir işleme almaz.</span><span class="sxs-lookup"><span data-stu-id="f5c39-224">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="f5c39-225">`<LastUsedBuildConfiguration>`Özelliği olarak ayarlanır `Release` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-225">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="f5c39-226">Visual Studio 'dan yayımlarken değeri, `<LastUsedBuildConfiguration>` Yayımlama işlemi başlatıldığında değeri kullanılarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-226">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="f5c39-227">`<LastUsedBuildConfiguration>`özeldir ve içeri aktarılan MSBuild dosyasında geçersiz kılınmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-227">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="f5c39-228">Ancak, bu özellik aşağıdaki yaklaşımlardan birini kullanarak komut satırından geçersiz kılınabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-228">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="f5c39-229">.NET Core CLI kullanarak:</span><span class="sxs-lookup"><span data-stu-id="f5c39-229">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="f5c39-230">MSBuild 'i kullanma:</span><span class="sxs-lookup"><span data-stu-id="f5c39-230">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="f5c39-231">Daha fazla bilgi için bkz. [MSBuild: yapılandırma özelliğini ayarlama](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5c39-231">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="f5c39-232">Komut satırından bir MSDeploy uç noktasına yayımlama</span><span class="sxs-lookup"><span data-stu-id="f5c39-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="f5c39-233">Aşağıdaki örnek, *AzureWebApp*adlı Visual Studio tarafından oluşturulan bir ASP.NET Core Web uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="f5c39-234">Visual Studio ile bir Azure Apps yayımlama profili eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="f5c39-235">Profil oluşturma hakkında daha fazla bilgi için, [Yayımlama profilleri](#publish-profiles) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="f5c39-236">Uygulamayı bir yayımlama profili kullanarak dağıtmak için, `msbuild` bir Visual Studio **Geliştirici komut istemi**komutunu yürütün.</span><span class="sxs-lookup"><span data-stu-id="f5c39-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="f5c39-237">Komut istemi, Windows görev çubuğundaki **Başlat** menüsünün *Visual Studio* klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="f5c39-238">Daha kolay erişim için, Visual Studio 'daki **Araçlar** menüsüne komut istemi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f5c39-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="f5c39-239">Daha fazla bilgi için bkz. [Visual Studio için geliştirici komut istemi](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="f5c39-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="f5c39-240">MSBuild aşağıdaki komut sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="f5c39-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="f5c39-241">{PATH}: uygulamanın proje dosyasının yolu.</span><span class="sxs-lookup"><span data-stu-id="f5c39-241">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="f5c39-242">{PROFILE}: yayımlama profilinin adı.</span><span class="sxs-lookup"><span data-stu-id="f5c39-242">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="f5c39-243">{USERNAME}: MSDeploy Kullanıcı adı.</span><span class="sxs-lookup"><span data-stu-id="f5c39-243">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="f5c39-244">{USERNAME}, yayımlama profilinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="f5c39-245">{PASSWORD}: MSDeploy parolası.</span><span class="sxs-lookup"><span data-stu-id="f5c39-245">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="f5c39-246">{PROFILE} öğesinden {PASSWORD} öğesini edinin *. PublishSettings* dosyası.</span><span class="sxs-lookup"><span data-stu-id="f5c39-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="f5c39-247">' Nı indirin *. PublishSettings* dosyası şunlardan biri:</span><span class="sxs-lookup"><span data-stu-id="f5c39-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="f5c39-248">**Çözüm Gezgini**: **View**  >  **bulut Gezginini**görüntüle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-248">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="f5c39-249">Azure aboneliğinize bağlanın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="f5c39-250">**Uygulama hizmetleri**'ni açın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-250">Open **App Services**.</span></span> <span data-ttu-id="f5c39-251">Uygulamaya sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-251">Right-click the app.</span></span> <span data-ttu-id="f5c39-252">**Yayımlama profilini indir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="f5c39-253">Azure portal: Web uygulamasının **genel bakış** panelinde **Yayımlama profilini al** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="f5c39-254">Aşağıdaki örnek, *AzureWebApp-Web dağıtımı*adlı bir yayımlama profili kullanır:</span><span class="sxs-lookup"><span data-stu-id="f5c39-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="f5c39-255">Bir yayımlama profili, Windows komut kabuğu 'ndan .NET Core CLI [DotNet MSBuild](/dotnet/core/tools/dotnet-msbuild) komutuyla da kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-255">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="f5c39-256">`dotnet msbuild`Komut, platformlar arası bir komuttur ve macOS ve Linux üzerinde ASP.NET Core uygulamalar derleyebilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-256">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="f5c39-257">Ancak, macOS ve Linux 'ta MSBuild, bir uygulamayı Azure 'a veya diğer MSDeploy uç noktalarına dağıtmıyor.</span><span class="sxs-lookup"><span data-stu-id="f5c39-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="f5c39-258">Ortamı ayarlama</span><span class="sxs-lookup"><span data-stu-id="f5c39-258">Set the environment</span></span>

<span data-ttu-id="f5c39-259">`<EnvironmentName>`Uygulamanın [ortamını](xref:fundamentals/environments)ayarlamak için Publish profile (*. pubxml*) veya proje dosyasına özelliği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-259">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="f5c39-260">*Web. config* dönüştürmelerine ihtiyaç duyuyorsanız (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak), bkz <xref:host-and-deploy/iis/transform-webconfig> ..</span><span class="sxs-lookup"><span data-stu-id="f5c39-260">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="f5c39-261">Dosyaları Dışla</span><span class="sxs-lookup"><span data-stu-id="f5c39-261">Exclude files</span></span>

<span data-ttu-id="f5c39-262">ASP.NET Core Web Apps yayımlandığında, aşağıdaki varlıklar dahil edilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-262">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="f5c39-263">Yapı yapıtları</span><span class="sxs-lookup"><span data-stu-id="f5c39-263">Build artifacts</span></span>
* <span data-ttu-id="f5c39-264">Aşağıdaki glob desenleriyle eşleşen klasörler ve dosyalar:</span><span class="sxs-lookup"><span data-stu-id="f5c39-264">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="f5c39-265">`**\*.config`(örneğin, *Web. config*)</span><span class="sxs-lookup"><span data-stu-id="f5c39-265">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="f5c39-266">`**\*.json`(örneğin, *appSettings. JSON*)</span><span class="sxs-lookup"><span data-stu-id="f5c39-266">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="f5c39-267">MSBuild, [Glob desenlerini](https://gruntjs.com/configuring-tasks#globbing-patterns)destekler.</span><span class="sxs-lookup"><span data-stu-id="f5c39-267">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="f5c39-268">Örneğin, aşağıdaki öğe, `<Content>` metin (*. txt*) dosyalarının *wwwroot\content* klasörü ve alt klasörlerinde kopyalanmasını bastırır:</span><span class="sxs-lookup"><span data-stu-id="f5c39-268">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="f5c39-269">Önceki biçimlendirme bir yayımlama profiline veya *. csproj* dosyasına eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-269">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="f5c39-270">*. Csproj* dosyasına eklendiğinde, kural projedeki tüm yayımlama profillerine eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-270">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="f5c39-271">Aşağıdaki `<MsDeploySkipRules>` öğe, tüm dosyaları *wwwroot\content* klasöründen dışlar:</span><span class="sxs-lookup"><span data-stu-id="f5c39-271">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="f5c39-272">`<MsDeploySkipRules>`dağıtım sitesinden *atlama* hedeflerini silmez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-272">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="f5c39-273">`<Content>`hedeflenen dosya ve klasörler dağıtım sitesinden silinir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-273">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="f5c39-274">Örneğin, dağıtılan bir Web uygulamasının aşağıdaki dosyalar olduğunu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="f5c39-274">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="f5c39-275">*Görünümler/Home/about1. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f5c39-275">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="f5c39-276">*Görünümler/Home/About2. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f5c39-276">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="f5c39-277">*Görünümler/Home/About3. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f5c39-277">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="f5c39-278">Aşağıdaki `<MsDeploySkipRules>` öğeler eklenirse, bu dosyalar dağıtım sitesinde silinmez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-278">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="f5c39-279">Önceki `<MsDeploySkipRules>` öğeler *Atlanan* dosyaların dağıtılmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="f5c39-279">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="f5c39-280">Dağıtıldıktan sonra bu dosyaları silmez.</span><span class="sxs-lookup"><span data-stu-id="f5c39-280">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="f5c39-281">Aşağıdaki `<Content>` öğe, dağıtım sitesindeki hedeflenen dosyaları siler:</span><span class="sxs-lookup"><span data-stu-id="f5c39-281">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="f5c39-282">Önceki öğeyle komut satırı dağıtımı kullanmak `<Content>` , aşağıdaki çıkışın bir varyasyonunu verir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-282">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="f5c39-283">Dosyaları dahil etme</span><span class="sxs-lookup"><span data-stu-id="f5c39-283">Include files</span></span>

<span data-ttu-id="f5c39-284">Aşağıdaki bölümlerde, yayımlama zamanında dosya ekleme için farklı yaklaşımlar ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-284">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="f5c39-285">[Genel dosya ekleme](#general-file-inclusion) bölümü, `DotNetPublishFiles` [Web SDK 'sında](xref:razor-pages/web-sdk)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-285">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="f5c39-286">[Seçmeli dosya ekleme](#selective-file-inclusion) bölümü, `ResolvedFileToPublish` [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)bir Yayımla hedefi dosyası tarafından belirtilen öğesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f5c39-286">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="f5c39-287">Web SDK .NET Core SDK bağlı olduğundan, her iki öğe bir ASP.NET Core projesinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-287">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="f5c39-288">Genel dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="f5c39-288">General file inclusion</span></span>

<span data-ttu-id="f5c39-289">Aşağıdaki örnek öğesi, `<ItemGroup>` proje dizininin dışında bulunan bir klasörü yayımlanmış sitenin bir klasörüne kopyalamayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-289">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="f5c39-290">Aşağıdaki biçimlendirmeye eklenen tüm dosyalar `<ItemGroup>` Varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-290">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="f5c39-291">Yukarıdaki biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="f5c39-291">The preceding markup:</span></span>

* <span data-ttu-id="f5c39-292">*. Csproj* dosyasına veya yayımlama profiline eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-292">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="f5c39-293">*. Csproj* dosyasına eklenirse, bu, projedeki her bir yayımlama profiline eklenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-293">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="f5c39-294">`_CustomFiles`Özniteliğin glob düzeniyle eşleşen dosyaları depolamak için bir öğe bildirir `Include` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-294">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="f5c39-295">Düzende başvurulan *görüntüler* klasörü, proje dizininin dışında bulunur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-295">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="f5c39-296">Adlı [ayrılmış bir özellik](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), `$(MSBuildProjectDirectory)` Proje dosyasının mutlak yoluna çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-296">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="f5c39-297">Öğe için dosyaların bir listesini sağlar `DotNetPublishFiles` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-297">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="f5c39-298">Varsayılan olarak, öğenin `<DestinationRelativePath>` öğesi boştur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-298">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="f5c39-299">Varsayılan değer, biçimlendirmede geçersiz kılınır ve gibi [iyi bilinen öğe meta verilerini](/visualstudio/msbuild/msbuild-well-known-item-metadata) kullanır `%(RecursiveDir)` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-299">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="f5c39-300">İç metin, yayımlanan sitenin *Wwwroot/görüntüler* klasörünü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f5c39-300">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="f5c39-301">Seçmeli dosya ekleme</span><span class="sxs-lookup"><span data-stu-id="f5c39-301">Selective file inclusion</span></span>

<span data-ttu-id="f5c39-302">Aşağıdaki örnekte vurgulanan biçimlendirme şunları göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="f5c39-302">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="f5c39-303">Projenin dışında bulunan bir dosyayı yayınlanan sitenin *Wwwroot* klasörüne kopyalama.</span><span class="sxs-lookup"><span data-stu-id="f5c39-303">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="f5c39-304">*ReadMe2.MD* dosyasının adı korunur.</span><span class="sxs-lookup"><span data-stu-id="f5c39-304">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="f5c39-305">*Wwwroot\content* klasörü dışlanıyor.</span><span class="sxs-lookup"><span data-stu-id="f5c39-305">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="f5c39-306">*Views\home\about2,cshtml*hariç tutulanıyor.</span><span class="sxs-lookup"><span data-stu-id="f5c39-306">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="f5c39-307">Yukarıdaki örnek, `ResolvedFileToPublish` varsayılan davranışı özniteliğinde belirtilen dosyaları her zaman yayımlanan siteye kopyalamak için olan öğesini kullanır `Include` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-307">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="f5c39-308">`<CopyToPublishDirectory>`Ya da ' ın iç metniyle bir alt öğe ekleyerek varsayılan davranışı geçersiz kılın `Never` `PreserveNewest` .</span><span class="sxs-lookup"><span data-stu-id="f5c39-308">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="f5c39-309">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-309">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="f5c39-310">Daha fazla dağıtım örneği için bkz. [Web SDK deposu Benioku dosyası](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="f5c39-310">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="f5c39-311">Yayımlamadan önce veya sonra bir hedef Çalıştır</span><span class="sxs-lookup"><span data-stu-id="f5c39-311">Run a target before or after publishing</span></span>

<span data-ttu-id="f5c39-312">Yerleşik `BeforePublish` ve `AfterPublish` hedefler, yayımlama hedefinden önce veya sonra bir hedef yürütür.</span><span class="sxs-lookup"><span data-stu-id="f5c39-312">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="f5c39-313">Aşağıdaki öğeleri yayımlama profiline, yayımlamadan önce ve sonra da konsol iletilerini günlüğe kaydetmek için ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-313">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="f5c39-314">Güvenilmeyen bir sertifikayı kullanarak bir sunucuya yayımlama</span><span class="sxs-lookup"><span data-stu-id="f5c39-314">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="f5c39-315">`<AllowUntrustedCertificate>`Değerini yayımlama profiline değeri olan özelliği ekleyin `True` :</span><span class="sxs-lookup"><span data-stu-id="f5c39-315">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="f5c39-316">Kudu hizmeti</span><span class="sxs-lookup"><span data-stu-id="f5c39-316">The Kudu service</span></span>

<span data-ttu-id="f5c39-317">Azure App Service Web uygulaması dağıtımında dosyaları görüntülemek için [kudu hizmetini](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)kullanın.</span><span class="sxs-lookup"><span data-stu-id="f5c39-317">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="f5c39-318">`scm`Belirteci Web uygulaması adına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-318">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="f5c39-319">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f5c39-319">For example:</span></span>

| <span data-ttu-id="f5c39-320">URL</span><span class="sxs-lookup"><span data-stu-id="f5c39-320">URL</span></span>                                    | <span data-ttu-id="f5c39-321">Sonuç</span><span class="sxs-lookup"><span data-stu-id="f5c39-321">Result</span></span>       |
| ---
<span data-ttu-id="f5c39-322">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-323">'Blazor'</span></span>
- <span data-ttu-id="f5c39-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-324">'Identity'</span></span>
- <span data-ttu-id="f5c39-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-326">'Razor'</span></span>
- <span data-ttu-id="f5c39-327">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-328">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-329">'Blazor'</span></span>
- <span data-ttu-id="f5c39-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-330">'Identity'</span></span>
- <span data-ttu-id="f5c39-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-332">'Razor'</span></span>
- <span data-ttu-id="f5c39-333">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-334">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-335">'Blazor'</span></span>
- <span data-ttu-id="f5c39-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-336">'Identity'</span></span>
- <span data-ttu-id="f5c39-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-338">'Razor'</span></span>
- <span data-ttu-id="f5c39-339">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-340">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-341">'Blazor'</span></span>
- <span data-ttu-id="f5c39-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-342">'Identity'</span></span>
- <span data-ttu-id="f5c39-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-344">'Razor'</span></span>
- <span data-ttu-id="f5c39-345">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-346">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-347">'Blazor'</span></span>
- <span data-ttu-id="f5c39-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-348">'Identity'</span></span>
- <span data-ttu-id="f5c39-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-350">'Razor'</span></span>
- <span data-ttu-id="f5c39-351">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-352">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-353">'Blazor'</span></span>
- <span data-ttu-id="f5c39-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-354">'Identity'</span></span>
- <span data-ttu-id="f5c39-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-356">'Razor'</span></span>
- <span data-ttu-id="f5c39-357">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-358">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-359">'Blazor'</span></span>
- <span data-ttu-id="f5c39-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-360">'Identity'</span></span>
- <span data-ttu-id="f5c39-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-362">'Razor'</span></span>
- <span data-ttu-id="f5c39-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-364">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-365">'Blazor'</span></span>
- <span data-ttu-id="f5c39-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-366">'Identity'</span></span>
- <span data-ttu-id="f5c39-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-368">'Razor'</span></span>
- <span data-ttu-id="f5c39-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-370">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-371">'Blazor'</span></span>
- <span data-ttu-id="f5c39-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-372">'Identity'</span></span>
- <span data-ttu-id="f5c39-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-374">'Razor'</span></span>
- <span data-ttu-id="f5c39-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-376">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-377">'Blazor'</span></span>
- <span data-ttu-id="f5c39-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-378">'Identity'</span></span>
- <span data-ttu-id="f5c39-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-380">'Razor'</span></span>
- <span data-ttu-id="f5c39-381">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-382">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-383">'Blazor'</span></span>
- <span data-ttu-id="f5c39-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-384">'Identity'</span></span>
- <span data-ttu-id="f5c39-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-386">'Razor'</span></span>
- <span data-ttu-id="f5c39-387">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-388">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-389">'Blazor'</span></span>
- <span data-ttu-id="f5c39-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-390">'Identity'</span></span>
- <span data-ttu-id="f5c39-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-392">'Razor'</span></span>
- <span data-ttu-id="f5c39-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-394">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-395">'Blazor'</span></span>
- <span data-ttu-id="f5c39-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-396">'Identity'</span></span>
- <span data-ttu-id="f5c39-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-398">'Razor'</span></span>
- <span data-ttu-id="f5c39-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-400">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-401">'Blazor'</span></span>
- <span data-ttu-id="f5c39-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-402">'Identity'</span></span>
- <span data-ttu-id="f5c39-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-404">'Razor'</span></span>
- <span data-ttu-id="f5c39-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-406">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-407">'Blazor'</span></span>
- <span data-ttu-id="f5c39-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-408">'Identity'</span></span>
- <span data-ttu-id="f5c39-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-410">'Razor'</span></span>
- <span data-ttu-id="f5c39-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-412">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-413">'Blazor'</span></span>
- <span data-ttu-id="f5c39-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-414">'Identity'</span></span>
- <span data-ttu-id="f5c39-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-416">'Razor'</span></span>
- <span data-ttu-id="f5c39-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-418">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-419">'Blazor'</span></span>
- <span data-ttu-id="f5c39-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-420">'Identity'</span></span>
- <span data-ttu-id="f5c39-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-422">'Razor'</span></span>
- <span data-ttu-id="f5c39-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-423">'SignalR' uid:</span></span> 

<span data-ttu-id="f5c39-424">------------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-425">'Blazor'</span></span>
- <span data-ttu-id="f5c39-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-426">'Identity'</span></span>
- <span data-ttu-id="f5c39-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-428">'Razor'</span></span>
- <span data-ttu-id="f5c39-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-430">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-431">'Blazor'</span></span>
- <span data-ttu-id="f5c39-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-432">'Identity'</span></span>
- <span data-ttu-id="f5c39-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-434">'Razor'</span></span>
- <span data-ttu-id="f5c39-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-436">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-437">'Blazor'</span></span>
- <span data-ttu-id="f5c39-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-438">'Identity'</span></span>
- <span data-ttu-id="f5c39-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-440">'Razor'</span></span>
- <span data-ttu-id="f5c39-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f5c39-442">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f5c39-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f5c39-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-443">'Blazor'</span></span>
- <span data-ttu-id="f5c39-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f5c39-444">'Identity'</span></span>
- <span data-ttu-id="f5c39-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f5c39-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="f5c39-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f5c39-446">'Razor'</span></span>
- <span data-ttu-id="f5c39-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f5c39-447">'SignalR' uid:</span></span> 

<span data-ttu-id="f5c39-448">------ | | `http://mysite.azurewebsites.net/`     | Web uygulaması | | `http://mysite.scm.azurewebsites.net/` | Kudu hizmeti |</span><span class="sxs-lookup"><span data-stu-id="f5c39-448">------ | | `http://mysite.azurewebsites.net/`     | Web App      | | `http://mysite.scm.azurewebsites.net/` | Kudu service |</span></span>

<span data-ttu-id="f5c39-449">Dosyaları görüntülemek, düzenlemek, silmek veya eklemek için [hata ayıklama konsolu](https://github.com/projectkudu/kudu/wiki/Kudu-console) menü öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f5c39-449">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5c39-450">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f5c39-450">Additional resources</span></span>

* <span data-ttu-id="f5c39-451">[Web dağıtımı](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy), IIS sunucularına Web uygulamaları ve Web siteleri dağıtımını basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="f5c39-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="f5c39-452">[Web SDK GitHub deposu](https://github.com/aspnet/websdk/issues): dağıtım için dosya sorunları ve istek özellikleri.</span><span class="sxs-lookup"><span data-stu-id="f5c39-452">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="f5c39-453">Visual Studio 'dan bir Azure VM 'de ASP.NET Web uygulaması yayımlama</span><span class="sxs-lookup"><span data-stu-id="f5c39-453">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
