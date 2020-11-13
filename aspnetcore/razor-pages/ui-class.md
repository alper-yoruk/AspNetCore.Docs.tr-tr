---
title: :::no-loc(Razor):::ASP.NET Core ile sınıf kitaplıklarında yeniden kullanılabilir kullanıcı arabirimi
author: Rick-Anderson
description: :::no-loc(Razor):::ASP.NET Core bir sınıf kitaplığında kısmi görünümler kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturmayı açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/ui-class
ms.openlocfilehash: 0bfdb1932d829ec00c9de1bd38b7920cb1f40c51
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570178"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="33a0b-103">:::no-loc(Razor):::ASP.NET Core içindeki sınıf kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="33a0b-103">Create reusable UI using the :::no-loc(Razor)::: class library project in ASP.NET Core</span></span>

<span data-ttu-id="33a0b-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33a0b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33a0b-105">:::no-loc(Razor):::görünümler, sayfalar, denetleyiciler, sayfa modelleri, [ :::no-loc(Razor)::: Bileşenler](xref:blazor/components/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri bir :::no-loc(Razor)::: sınıf kitaplığı (RCL) halinde oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-105">:::no-loc(Razor)::: views, pages, controllers, page models, [:::no-loc(Razor)::: components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a :::no-loc(Razor)::: class library (RCL).</span></span> <span data-ttu-id="33a0b-106">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="33a0b-107">Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="33a0b-108">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya :::no-loc(Razor)::: sayfa bulunduğunda, :::no-loc(Razor)::: Web uygulamasındaki biçimlendirme ( *. cshtml* dosyası) öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-108">When a view, partial view, or :::no-loc(Razor)::: Page is found in both the web app and the RCL, the :::no-loc(Razor)::: markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="33a0b-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33a0b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="33a0b-110">UI içeren bir sınıf kitaplığı oluşturma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="33a0b-110">Create a class library containing :::no-loc(Razor)::: UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33a0b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33a0b-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="33a0b-112">Visual Studio 'dan **Yeni bir proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="33a0b-113">**:::no-loc(Razor)::: Sınıf kitaplığı** > **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-113">Select **:::no-loc(Razor)::: Class Library** > **Next**.</span></span>
* <span data-ttu-id="33a0b-114">Kitaplığı adlandırın (örneğin, " :::no-loc(Razor)::: ClassLib"), > **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="33a0b-114">Name the library (for example, ":::no-loc(Razor):::ClassLib"), > **Create**.</span></span> <span data-ttu-id="33a0b-115">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="33a0b-116">Görünümleri desteketmeniz gerekiyorsa **destek sayfaları ve görünümleri '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="33a0b-117">Varsayılan olarak, yalnızca :::no-loc(Razor)::: Sayfalar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-117">By default, only :::no-loc(Razor)::: Pages are supported.</span></span> <span data-ttu-id="33a0b-118">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-118">Select **Create**.</span></span>

<span data-ttu-id="33a0b-119">:::no-loc(Razor):::Sınıf kitaplığı (RCL) şablonu :::no-loc(Razor)::: Varsayılan olarak varsayılan olarak bileşen geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-119">The :::no-loc(Razor)::: class library (RCL) template defaults to :::no-loc(Razor)::: component development by default.</span></span> <span data-ttu-id="33a0b-120">**Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.</span><span class="sxs-lookup"><span data-stu-id="33a0b-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33a0b-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33a0b-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33a0b-122">Komut satırından komutunu çalıştırın `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="33a0b-123">Örnek:</span><span class="sxs-lookup"><span data-stu-id="33a0b-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o :::no-loc(Razor):::UIClassLib
```

<span data-ttu-id="33a0b-124">:::no-loc(Razor):::Sınıf kitaplığı (RCL) şablonu :::no-loc(Razor)::: Varsayılan olarak varsayılan olarak bileşen geliştirme olur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-124">The :::no-loc(Razor)::: class library (RCL) template defaults to :::no-loc(Razor)::: component development by default.</span></span> <span data-ttu-id="33a0b-125">`--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views` Sayfalar ve görünümler için destek sağlamak üzere seçeneğini () geçirin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="33a0b-126">Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="33a0b-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="33a0b-127">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="33a0b-128">:::no-loc(Razor):::RCL 'ye dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-128">Add :::no-loc(Razor)::: files to the RCL.</span></span>

<span data-ttu-id="33a0b-129">ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="33a0b-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="33a0b-130">' De içeriğini kullanıma sunan bir RCL oluşturmak için [RCL Pages düzenine](#rcl-pages-layout) bakın `~/Pages` `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="33a0b-131">RCL içeriğine başvur</span><span class="sxs-lookup"><span data-stu-id="33a0b-131">Reference RCL content</span></span>

<span data-ttu-id="33a0b-132">RCL 'ye şu şekilde başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="33a0b-133">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="33a0b-133">NuGet package.</span></span> <span data-ttu-id="33a0b-134">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="33a0b-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="33a0b-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="33a0b-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="33a0b-136">Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="33a0b-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="33a0b-137">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="33a0b-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="33a0b-138">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya :::no-loc(Razor)::: sayfa bulunduğunda, :::no-loc(Razor)::: Web uygulamasındaki biçimlendirme ( *. cshtml* dosyası) öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-138">When a view, partial view, or :::no-loc(Razor)::: Page is found in both the web app and the RCL, the :::no-loc(Razor)::: markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="33a0b-139">Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="33a0b-140">Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="33a0b-141">*:::no-loc(Razor)::: Uıısslib/Areas/Myfeature/Pages/shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/pages/Shared/_Message. cshtml* 'ye kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-141">Copy the *:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="33a0b-142">Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="33a0b-143">Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="33a0b-144">RCL sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="33a0b-144">RCL Pages layout</span></span>

<span data-ttu-id="33a0b-145">RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33a0b-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="33a0b-146">*:::no-loc(Razor):::Uıısslib/sayfalar*</span><span class="sxs-lookup"><span data-stu-id="33a0b-146">*:::no-loc(Razor):::UIClassLib/Pages*</span></span>
* <span data-ttu-id="33a0b-147">*:::no-loc(Razor):::Uıısslib/sayfalar/paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="33a0b-147">*:::no-loc(Razor):::UIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="33a0b-148">*:::no-loc(Razor)::: Uıısslib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33a0b-148">Suppose *:::no-loc(Razor):::UIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="33a0b-149">`<partial>`Etiketler *_Layout. cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="33a0b-150">Statik varlıklar içeren bir RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="33a0b-150">Create an RCL with static assets</span></span>

<span data-ttu-id="33a0b-151">RCL, RCL 'nin RCL veya tüketen uygulaması tarafından başvurulabilen, yardımcı statik varlıklar gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="33a0b-152">ASP.NET Core, tüketen bir uygulama tarafından kullanılabilen statik varlıkları içeren RCLs oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="33a0b-153">Yardımcı varlıkları RCL 'nin bir parçası olarak dahil etmek için, sınıf kitaplığında bir *Wwwroot* klasörü oluşturun ve gerekli dosyaları bu klasöre ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="33a0b-154">RCL 'yi paketleyerek, *Wwwroot* klasöründeki tüm yardımcı varlıklar pakete otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="33a0b-155">`dotnet pack`NuGet.exe sürümü yerine komutunu kullanın `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="33a0b-156">Statik varlıkları hariç tut</span><span class="sxs-lookup"><span data-stu-id="33a0b-156">Exclude static assets</span></span>

<span data-ttu-id="33a0b-157">Statik varlıkları dışlamak için, istenen dışlama yolunu `$(DefaultItemExcludes)` Proje dosyasındaki özellik grubuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="33a0b-158">Girişleri noktalı virgül () ile ayırın `;` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="33a0b-159">Aşağıdaki örnekte, *Wwwroot* klasöründeki *lib. css* stil sayfası statik bir varlık olarak değerlendirilmez ve yayımlanan RCL 'ye dahil değildir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="33a0b-160">TypeScript tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="33a0b-160">Typescript integration</span></span>

<span data-ttu-id="33a0b-161">TypeScript dosyalarını RCL 'ye eklemek için:</span><span class="sxs-lookup"><span data-stu-id="33a0b-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="33a0b-162">TypeScript dosyalarını ( *. TS* ) *Wwwroot* klasörünün dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-162">Place the TypeScript files ( *.ts* ) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="33a0b-163">Örneğin, dosyaları bir *istemci* klasörüne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="33a0b-164">*Wwwroot* klasörü için TypeScript derleme çıkışını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="33a0b-165">`TypescriptOutDir`Proje dosyasındaki öğesinin içindeki özelliğini ayarlayın `PropertyGroup` :</span><span class="sxs-lookup"><span data-stu-id="33a0b-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="33a0b-166">`ResolveCurrentProjectStaticWebAssets`Proje dosyasında bir öğesinin içine aşağıdaki hedefi ekleyerek TypeScript hedefini hedefin bağımlılığı olarak ekleyin `PropertyGroup` :</span><span class="sxs-lookup"><span data-stu-id="33a0b-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="33a0b-167">Başvurulan bir RCL 'den içerik tüketme</span><span class="sxs-lookup"><span data-stu-id="33a0b-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="33a0b-168">RCL 'nin *Wwwroot* klasörüne eklenen dosyalar, ön ek altında RCL veya tüketen uygulamaya sunulur `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="33a0b-169">Örneğin, adlı bir kitaplık *:::no-loc(Razor)::: . Class. lib* , konumundaki statik içeriğe yol açmaya neden olur `_content/:::no-loc(Razor):::.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-169">For example, a library named *:::no-loc(Razor):::.Class.Lib* results in a path to static content at `_content/:::no-loc(Razor):::.Class.Lib/`.</span></span> <span data-ttu-id="33a0b-170">Bir NuGet paketi üretilirken ve derleme adı paket KIMLIĞIYLE aynı değilse, için paket KIMLIĞINI kullanın `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="33a0b-171">Tüketen uygulama,,, `<script>` `<style>` `<img>` ve diğer HTML etiketleriyle kitaplık tarafından sunulan statik varlıklara başvurur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="33a0b-172">Kullanan uygulamada [statik dosya desteğinin](xref:fundamentals/static-files) etkinleştirilmesi gerekir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="33a0b-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="33a0b-173">Yapı çıktısından () kullanan uygulamayı çalıştırırken `dotnet run` , varsayılan olarak, statik Web varlıkları geliştirme ortamında etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="33a0b-174">Derleme çıktılarından çalışırken diğer ortamlardaki varlıkları desteklemek için, `UseStaticWebAssets` *program.cs* içindeki konak Oluşturucu 'da çağırın:</span><span class="sxs-lookup"><span data-stu-id="33a0b-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="33a0b-175">`UseStaticWebAssets`Yayımlanan çıktısından () bir uygulama çalıştırılırken çağırma gerekmez `dotnet publish` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="33a0b-176">Çoklu projeli geliştirme akışı</span><span class="sxs-lookup"><span data-stu-id="33a0b-176">Multi-project development flow</span></span>

<span data-ttu-id="33a0b-177">Kullanan uygulama şu şekilde çalışır:</span><span class="sxs-lookup"><span data-stu-id="33a0b-177">When the consuming app runs:</span></span>

* <span data-ttu-id="33a0b-178">RCL içindeki varlıklar özgün klasörlerinde kalır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="33a0b-179">Varlıklar, tüketim uygulamasına taşınmaz.</span><span class="sxs-lookup"><span data-stu-id="33a0b-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="33a0b-180">RCL 'nin *Wwwroot* klasörü içindeki tüm değişiklikler, RCL yeniden oluşturulduktan ve tüketen uygulamayı yeniden oluşturmadan önce tüketen uygulamaya yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="33a0b-181">RCL yapılandırıldığında, statik Web varlık konumlarını açıklayan bir bildirim oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="33a0b-182">Tüketen uygulama, başvurulan proje ve paketlerden varlıkları kullanmak için çalışma zamanında bildirimi okur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="33a0b-183">Bir RCL 'ye yeni bir varlık eklendiğinde, bir uygulamanın yeni varlığa erişebilmesi için bildirim güncellemek üzere RCL 'nin yeniden oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="33a0b-184">Yayımlama</span><span class="sxs-lookup"><span data-stu-id="33a0b-184">Publish</span></span>

<span data-ttu-id="33a0b-185">Uygulama yayımlandığında, tüm başvurulan projeler ve paketlerin yardımcı varlıkları altında yayımlanan uygulamanın *Wwwroot* klasörüne kopyalanır `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="33a0b-186">:::no-loc(Razor):::görünümler, sayfalar, denetleyiciler, sayfa modelleri, [ :::no-loc(Razor)::: Bileşenler](xref:blazor/components/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri bir :::no-loc(Razor)::: sınıf kitaplığı (RCL) halinde oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-186">:::no-loc(Razor)::: views, pages, controllers, page models, [:::no-loc(Razor)::: components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a :::no-loc(Razor)::: class library (RCL).</span></span> <span data-ttu-id="33a0b-187">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="33a0b-188">Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="33a0b-189">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya :::no-loc(Razor)::: sayfa bulunduğunda, :::no-loc(Razor)::: Web uygulamasındaki biçimlendirme ( *. cshtml* dosyası) öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-189">When a view, partial view, or :::no-loc(Razor)::: Page is found in both the web app and the RCL, the :::no-loc(Razor)::: markup ( *.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="33a0b-190">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33a0b-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="33a0b-191">UI içeren bir sınıf kitaplığı oluşturma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="33a0b-191">Create a class library containing :::no-loc(Razor)::: UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33a0b-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33a0b-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="33a0b-193">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="33a0b-194">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33a0b-195">Kitaplığı adlandırın (örneğin, " :::no-loc(Razor)::: ClassLib") > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="33a0b-195">Name the library (for example, ":::no-loc(Razor):::ClassLib") > **OK**.</span></span> <span data-ttu-id="33a0b-196">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="33a0b-197">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33a0b-198">**:::no-loc(Razor)::: Sınıf kitaplığı** > **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-198">Select **:::no-loc(Razor)::: Class Library** > **OK**.</span></span>

<span data-ttu-id="33a0b-199">RCL aşağıdaki proje dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/:::no-loc(Razor):::UIClassLib/:::no-loc(Razor):::UIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="33a0b-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33a0b-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33a0b-201">Komut satırından komutunu çalıştırın `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="33a0b-202">Örnek:</span><span class="sxs-lookup"><span data-stu-id="33a0b-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o :::no-loc(Razor):::UIClassLib
```

<span data-ttu-id="33a0b-203">Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="33a0b-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="33a0b-204">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="33a0b-205">:::no-loc(Razor):::RCL 'ye dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-205">Add :::no-loc(Razor)::: files to the RCL.</span></span>

<span data-ttu-id="33a0b-206">ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="33a0b-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="33a0b-207">' De içeriğini kullanıma sunan bir RCL oluşturmak için [RCL Pages düzenine](#rcl-pages-layout) bakın `~/Pages` `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="33a0b-208">RCL içeriğine başvur</span><span class="sxs-lookup"><span data-stu-id="33a0b-208">Reference RCL content</span></span>

<span data-ttu-id="33a0b-209">RCL 'ye şu şekilde başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="33a0b-210">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="33a0b-210">NuGet package.</span></span> <span data-ttu-id="33a0b-211">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="33a0b-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="33a0b-212">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="33a0b-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="33a0b-213">Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="33a0b-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="33a0b-214">İzlenecek yol: bir RCL projesi oluşturma ve bir :::no-loc(Razor)::: Sayfalar projesinden kullanma</span><span class="sxs-lookup"><span data-stu-id="33a0b-214">Walkthrough: Create an RCL project and use from a :::no-loc(Razor)::: Pages project</span></span>

<span data-ttu-id="33a0b-215">[Tüm projeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) indirebilir ve oluşturmak yerine test edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="33a0b-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="33a0b-216">Örnek indirme, projenin test olmasını kolaylaştıran ek kod ve bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="33a0b-217">Yükleme örnekleri ve adım adım yönergeler hakkındaki açıklamalarınızla [Bu GitHub sorunuyla](https://github.com/dotnet/AspNetCore.Docs/issues/6098) ilgili geri bildirimde bulunun.</span><span class="sxs-lookup"><span data-stu-id="33a0b-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="33a0b-218">İndirme uygulamasını test etme</span><span class="sxs-lookup"><span data-stu-id="33a0b-218">Test the download app</span></span>

<span data-ttu-id="33a0b-219">Tamamlanmış uygulamayı indirmediyseniz ve gözden geçirme projesi oluşturmak istiyorsanız, [sonraki bölüme](#create-an-rcl)atlayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33a0b-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33a0b-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33a0b-221">Visual Studio 'da *. sln* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="33a0b-222">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33a0b-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33a0b-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33a0b-224">*CLI* dizinindeki bir komut isteminden RCL ve Web uygulamasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="33a0b-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="33a0b-225">*WebApp1* dizinine gidin ve uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="33a0b-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="33a0b-226">[Test WebApp1](#test-webapp1) içindeki yönergeleri izleyin</span><span class="sxs-lookup"><span data-stu-id="33a0b-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="33a0b-227">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="33a0b-227">Create an RCL</span></span>

<span data-ttu-id="33a0b-228">Bu bölümde bir RCL oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-228">In this section, an RCL is created.</span></span> <span data-ttu-id="33a0b-229">:::no-loc(Razor)::: dosyalar RCL 'ye eklenir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-229">:::no-loc(Razor)::: files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33a0b-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33a0b-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33a0b-231">RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33a0b-231">Create the RCL project:</span></span>

* <span data-ttu-id="33a0b-232">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="33a0b-233">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33a0b-234">Uygulamayı **:::no-loc(Razor)::: uıısslib** olarak adlandırın > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33a0b-234">Name the app **:::no-loc(Razor):::UIClassLib** > **OK**.</span></span>
* <span data-ttu-id="33a0b-235">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33a0b-236">**:::no-loc(Razor)::: Sınıf kitaplığı** > **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-236">Select **:::no-loc(Razor)::: Class Library** > **OK**.</span></span>
* <span data-ttu-id="33a0b-237">:::no-loc(Razor)::: *:::no-loc(Razor)::: Uıısslib/Areas/Myfeature/Pages/Shared/_Message. cshtml* adlı kısmi bir görünüm dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-237">Add a :::no-loc(Razor)::: partial view file named *:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33a0b-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33a0b-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33a0b-239">Komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="33a0b-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o :::no-loc(Razor):::UIClassLib
dotnet new page -n _Message -np -o :::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o :::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="33a0b-240">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="33a0b-240">The preceding commands:</span></span>

* <span data-ttu-id="33a0b-241">`:::no-loc(Razor):::UIClassLib`RCL 'yi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-241">Creates the `:::no-loc(Razor):::UIClassLib` RCL.</span></span>
* <span data-ttu-id="33a0b-242">Bir :::no-loc(Razor)::: _Message sayfası oluşturur ve RCL 'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="33a0b-242">Creates a :::no-loc(Razor)::: _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="33a0b-243">`-np`Parametresi, sayfasını bir olmadan oluşturur `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="33a0b-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="33a0b-244">Bir [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL 'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="33a0b-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="33a0b-245">*_ViewStart. cshtml* dosyası, :::no-loc(Razor)::: Sayfalar projesinin yerleşimini (bir sonraki bölüme eklenen) kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-245">The *_ViewStart.cshtml* file is required to use the layout of the :::no-loc(Razor)::: Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="33a0b-246">:::no-loc(Razor):::Projeye dosya ve klasör ekleme</span><span class="sxs-lookup"><span data-stu-id="33a0b-246">Add :::no-loc(Razor)::: files and folders to the project</span></span>

* <span data-ttu-id="33a0b-247">*:::no-loc(Razor)::: Uıısslib/Areas/Myfeature/Pages/Shared/_Message. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="33a0b-247">Replace the markup in *:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="33a0b-248">*:::no-loc(Razor)::: Uıısslib/Areas/Myfeature/Pages/Sayfa1. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="33a0b-248">Replace the markup in *:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="33a0b-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` kısmi görünümü kullanmak için gerekir ( `<partial name="_Message" />` ).</span><span class="sxs-lookup"><span data-stu-id="33a0b-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="33a0b-250">`@addTagHelper`Yönergesini eklemek yerine bir *_ViewImports. cshtml* dosyası ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="33a0b-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="33a0b-251">Örnek:</span><span class="sxs-lookup"><span data-stu-id="33a0b-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o :::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="33a0b-252">*_ViewImports. cshtml* hakkında daha fazla bilgi için bkz. [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="33a0b-252">For more information on *_ViewImports.cshtml* , see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="33a0b-253">Derleyici hatası olmadığını doğrulamak için sınıf kitaplığı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33a0b-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build :::no-loc(Razor):::UIClassLib
  ```

<span data-ttu-id="33a0b-254">Derleme çıktısı *:::no-loc(Razor):::UIClassLib.dll* ve *:::no-loc(Razor):::UIClassLib.Views.dll* içerir.</span><span class="sxs-lookup"><span data-stu-id="33a0b-254">The build output contains *:::no-loc(Razor):::UIClassLib.dll* and *:::no-loc(Razor):::UIClassLib.Views.dll*.</span></span> <span data-ttu-id="33a0b-255">*:::no-loc(Razor):::UIClassLib.Views.dll* derlenen içeriği içerir :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="33a0b-255">*:::no-loc(Razor):::UIClassLib.Views.dll* contains the compiled :::no-loc(Razor)::: content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="33a0b-256">:::no-loc(Razor):::Bir sayfalar projesinden UI kitaplığını kullanma :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="33a0b-256">Use the :::no-loc(Razor)::: UI library from a :::no-loc(Razor)::: Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33a0b-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33a0b-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33a0b-258">:::no-loc(Razor):::Sayfalar Web uygulaması oluşturma:</span><span class="sxs-lookup"><span data-stu-id="33a0b-258">Create the :::no-loc(Razor)::: Pages web app:</span></span>

* <span data-ttu-id="33a0b-259">**Çözüm Gezgini** , **Add** > **Yeni proje** Ekle > çözüme sağ tıklayın.  </span><span class="sxs-lookup"><span data-stu-id="33a0b-259">From **Solution Explorer** , right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="33a0b-260">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33a0b-261">Uygulamayı **WebApp1** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="33a0b-262">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33a0b-263">**Web uygulaması** > **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="33a0b-264">**Çözüm Gezgini** , **WebApp1** ' ye sağ tıklayın ve **Başlangıç projesi olarak ayarla** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-264">From **Solution Explorer** , right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="33a0b-265">**Çözüm Gezgini** , **WebApp1** ' ye sağ tıklayın ve **yapı bağımlılıkları** > **Proje bağımlılıkları** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-265">From **Solution Explorer** , right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="33a0b-266">**:::no-loc(Razor)::: Uıısslib** öğesini **WebApp1** bağımlılığı olarak denetleyin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-266">Check **:::no-loc(Razor):::UIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="33a0b-267">**Çözüm Gezgini** , **WebApp1** ' a sağ tıklayın ve başvuru **Ekle** ' yi seçin > **Reference**.</span><span class="sxs-lookup"><span data-stu-id="33a0b-267">From **Solution Explorer** , right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="33a0b-268">**Başvuru Yöneticisi** iletişim kutusunda **:::no-loc(Razor)::: uıısslib** Tamam ' ı işaretleyin > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33a0b-268">In the **Reference Manager** dialog, check **:::no-loc(Razor):::UIClassLib** > **OK**.</span></span>

<span data-ttu-id="33a0b-269">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33a0b-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="33a0b-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33a0b-271">Sayfalar :::no-loc(Razor)::: :::no-loc(Razor)::: uygulaması ve RCL içeren bir sayfa Web uygulaması ve bir çözüm dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33a0b-271">Create a :::no-loc(Razor)::: Pages web app and a solution file containing the :::no-loc(Razor)::: Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add :::no-loc(Razor):::UIClassLib
dotnet add WebApp1 reference :::no-loc(Razor):::UIClassLib
```

<span data-ttu-id="33a0b-272">Web uygulamasını derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="33a0b-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="33a0b-273">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="33a0b-273">Test WebApp1</span></span>

<span data-ttu-id="33a0b-274">`/MyFeature/Page1`UI sınıf kitaplığının kullanımda olduğunu doğrulamak için öğesine gidin :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="33a0b-274">Browse to `/MyFeature/Page1` to verify that the :::no-loc(Razor)::: UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="33a0b-275">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="33a0b-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="33a0b-276">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya :::no-loc(Razor)::: sayfa bulunduğunda, :::no-loc(Razor)::: Web uygulamasındaki biçimlendirme ( *. cshtml* dosyası) öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="33a0b-276">When a view, partial view, or :::no-loc(Razor)::: Page is found in both the web app and the RCL, the :::no-loc(Razor)::: markup ( *.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="33a0b-277">Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.</span><span class="sxs-lookup"><span data-stu-id="33a0b-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="33a0b-278">Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="33a0b-279">*:::no-loc(Razor)::: Uıısslib/Areas/Myfeature/Pages/shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/pages/Shared/_Message. cshtml* 'ye kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-279">Copy the *:::no-loc(Razor):::UIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="33a0b-280">Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="33a0b-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="33a0b-281">Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="33a0b-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="33a0b-282">RCL sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="33a0b-282">RCL Pages layout</span></span>

<span data-ttu-id="33a0b-283">RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="33a0b-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="33a0b-284">*:::no-loc(Razor):::Uıısslib/sayfalar*</span><span class="sxs-lookup"><span data-stu-id="33a0b-284">*:::no-loc(Razor):::UIClassLib/Pages*</span></span>
* <span data-ttu-id="33a0b-285">*:::no-loc(Razor):::Uıısslib/sayfalar/paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="33a0b-285">*:::no-loc(Razor):::UIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="33a0b-286">*:::no-loc(Razor)::: Uıısslib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33a0b-286">Suppose *:::no-loc(Razor):::UIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="33a0b-287">`<partial>`Etiketler *_Layout. cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="33a0b-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="33a0b-288">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="33a0b-288">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
