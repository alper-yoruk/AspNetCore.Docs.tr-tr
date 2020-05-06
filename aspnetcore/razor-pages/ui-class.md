---
title: ASP.NET Core Razor ile sınıf kitaplıklarında yeniden kullanılabilir kullanıcı arabirimi
author: Rick-Anderson
description: ASP.NET Core bir sınıf kitaplığında kısmi Razor görünümler kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturmayı açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 2c2a2c1e13b2d511ecf8c1c02c235192861fd486
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774281"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="2d522-103">ASP.NET Core 'de Razor Sınıf Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma</span><span class="sxs-lookup"><span data-stu-id="2d522-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="2d522-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2d522-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2d522-105">Razor görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri](xref:blazor/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri Razor sınıf kitaplığı 'nda (RCL) yerleşik olarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="2d522-106">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="2d522-107">Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="2d522-108">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="2d522-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="2d522-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d522-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="2d522-110">Razor Kullanıcı arabirimi içeren bir sınıf kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2d522-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d522-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d522-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2d522-112">Visual Studio 'dan **Yeni bir proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="2d522-113">**Razor sınıfı kitaplığı** > **İleri ' yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="2d522-114">Kitaplığı adlandırın (örneğin, "RazorClassLib"), > **Oluştur**.</span><span class="sxs-lookup"><span data-stu-id="2d522-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="2d522-115">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.</span><span class="sxs-lookup"><span data-stu-id="2d522-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="2d522-116">Görünümleri desteketmeniz gerekiyorsa **destek sayfaları ve görünümleri '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="2d522-117">Varsayılan olarak yalnızca Razor Pages desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2d522-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="2d522-118">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-118">Select **Create**.</span></span>

<span data-ttu-id="2d522-119">Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirmeyi varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="2d522-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="2d522-120">**Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.</span><span class="sxs-lookup"><span data-stu-id="2d522-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d522-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d522-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d522-122">Komut satırından komutunu çalıştırın `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="2d522-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="2d522-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2d522-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="2d522-124">Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirmeyi varsayılan olarak belirler.</span><span class="sxs-lookup"><span data-stu-id="2d522-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="2d522-125">Sayfalar ve `--support-pages-and-views` görünümler için`dotnet new razorclasslib --support-pages-and-views`destek sağlamak üzere seçeneğini () geçirin.</span><span class="sxs-lookup"><span data-stu-id="2d522-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="2d522-126">Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="2d522-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="2d522-127">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.</span><span class="sxs-lookup"><span data-stu-id="2d522-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="2d522-128">RCL 'ye Razor dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="2d522-129">ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="2d522-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="2d522-130">' De `~/Pages` içeriğini kullanıma sunan bir RCL oluşturmak Için [RCL Pages düzenine](#rcl-pages-layout) bakın. `~/Areas/Pages`</span><span class="sxs-lookup"><span data-stu-id="2d522-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="2d522-131">RCL içeriğine başvur</span><span class="sxs-lookup"><span data-stu-id="2d522-131">Reference RCL content</span></span>

<span data-ttu-id="2d522-132">RCL 'ye şu şekilde başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="2d522-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="2d522-133">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2d522-133">NuGet package.</span></span> <span data-ttu-id="2d522-134">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="2d522-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="2d522-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="2d522-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="2d522-136">Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="2d522-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="2d522-137">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="2d522-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="2d522-138">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="2d522-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="2d522-139">Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.</span><span class="sxs-lookup"><span data-stu-id="2d522-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="2d522-140">Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2d522-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="2d522-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/Pages/Shared/_Message. cshtml*'ye kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="2d522-142">Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d522-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="2d522-143">Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="2d522-144">RCL sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="2d522-144">RCL Pages layout</span></span>

<span data-ttu-id="2d522-145">RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="2d522-146">*RazorUIClassLib/sayfalar*</span><span class="sxs-lookup"><span data-stu-id="2d522-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="2d522-147">*RazorUIClassLib/sayfalar/paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="2d522-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="2d522-148">*RazorUIClassLib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2d522-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="2d522-149">`<partial>` Etiketler *_Layout. cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="2d522-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="2d522-150">Statik varlıklar içeren bir RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="2d522-150">Create an RCL with static assets</span></span>

<span data-ttu-id="2d522-151">RCL, RCL 'nin RCL veya tüketen uygulaması tarafından başvurulabilen, yardımcı statik varlıklar gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="2d522-152">ASP.NET Core, tüketen bir uygulama tarafından kullanılabilen statik varlıkları içeren RCLs oluşturulmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2d522-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="2d522-153">Yardımcı varlıkları RCL 'nin bir parçası olarak dahil etmek için, sınıf kitaplığında bir *Wwwroot* klasörü oluşturun ve gerekli dosyaları bu klasöre ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="2d522-154">RCL 'yi paketleyerek, *Wwwroot* klasöründeki tüm yardımcı varlıklar pakete otomatik olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="2d522-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="2d522-155">Statik varlıkları hariç tut</span><span class="sxs-lookup"><span data-stu-id="2d522-155">Exclude static assets</span></span>

<span data-ttu-id="2d522-156">Statik varlıkları dışlamak için, istenen dışlama yolunu proje dosyasındaki `$(DefaultItemExcludes)` özellik grubuna ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="2d522-157">Girişleri noktalı virgül (`;`) ile ayırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="2d522-158">Aşağıdaki örnekte, *Wwwroot* klasöründeki *lib. css* stil sayfası statik bir varlık olarak değerlendirilmez ve yayımlanan RCL 'ye dahil değildir:</span><span class="sxs-lookup"><span data-stu-id="2d522-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="2d522-159">TypeScript tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="2d522-159">Typescript integration</span></span>

<span data-ttu-id="2d522-160">TypeScript dosyalarını RCL 'ye eklemek için:</span><span class="sxs-lookup"><span data-stu-id="2d522-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="2d522-161">TypeScript dosyalarını (*. TS*) *Wwwroot* klasörünün dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d522-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="2d522-162">Örneğin, dosyaları bir *istemci* klasörüne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d522-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="2d522-163">*Wwwroot* klasörü için TypeScript derleme çıkışını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="2d522-164">Proje dosyasındaki `TypescriptOutDir` öğesinin `PropertyGroup` içindeki özelliğini ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="2d522-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="2d522-165">Proje dosyasında bir `PropertyGroup` öğesinin içine aşağıdaki hedefi ekleyerek TypeScript `ResolveCurrentProjectStaticWebAssets` hedefini hedefin bağımlılığı olarak ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2d522-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="2d522-166">Başvurulan bir RCL 'den içerik tüketme</span><span class="sxs-lookup"><span data-stu-id="2d522-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="2d522-167">RCL 'nin *Wwwroot* klasörüne eklenen dosyalar, ön ek `_content/{LIBRARY NAME}/`altında RCL veya tüketen uygulamaya sunulur.</span><span class="sxs-lookup"><span data-stu-id="2d522-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="2d522-168">Örneğin, *Razor. Class. lib* adlı bir kitaplık, konumundaki `_content/Razor.Class.Lib/`statik içeriğe bir yol ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="2d522-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="2d522-169">Bir NuGet paketi üretilirken ve derleme adı paket KIMLIĞIYLE aynı değilse, için `{LIBRARY NAME}`paket kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2d522-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="2d522-170">Tüketen uygulama `<script>`, `<style>` `<img>`,, ve diğer HTML etiketleriyle kitaplık tarafından sunulan statik varlıklara başvurur.</span><span class="sxs-lookup"><span data-stu-id="2d522-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="2d522-171">Kullanan uygulamada [statik dosya desteğinin](xref:fundamentals/static-files) etkinleştirilmesi gerekir `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2d522-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="2d522-172">Yapı çıktısından (`dotnet run`) kullanan uygulamayı çalıştırırken, varsayılan olarak, statik Web varlıkları geliştirme ortamında etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2d522-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="2d522-173">Derleme çıktılarından çalışırken diğer ortamlardaki varlıkları desteklemek için, *program.cs*içindeki konak `UseStaticWebAssets` Oluşturucu 'da çağırın:</span><span class="sxs-lookup"><span data-stu-id="2d522-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="2d522-174">Yayımlanan `UseStaticWebAssets` çıktısından (`dotnet publish`) bir uygulama çalıştırılırken çağırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2d522-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="2d522-175">Çoklu projeli geliştirme akışı</span><span class="sxs-lookup"><span data-stu-id="2d522-175">Multi-project development flow</span></span>

<span data-ttu-id="2d522-176">Kullanan uygulama şu şekilde çalışır:</span><span class="sxs-lookup"><span data-stu-id="2d522-176">When the consuming app runs:</span></span>

* <span data-ttu-id="2d522-177">RCL içindeki varlıklar özgün klasörlerinde kalır.</span><span class="sxs-lookup"><span data-stu-id="2d522-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="2d522-178">Varlıklar, tüketim uygulamasına taşınmaz.</span><span class="sxs-lookup"><span data-stu-id="2d522-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="2d522-179">RCL 'nin *Wwwroot* klasörü içindeki tüm değişiklikler, RCL yeniden oluşturulduktan ve tüketen uygulamayı yeniden oluşturmadan önce tüketen uygulamaya yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="2d522-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="2d522-180">RCL yapılandırıldığında, statik Web varlık konumlarını açıklayan bir bildirim oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2d522-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="2d522-181">Tüketen uygulama, başvurulan proje ve paketlerden varlıkları kullanmak için çalışma zamanında bildirimi okur.</span><span class="sxs-lookup"><span data-stu-id="2d522-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="2d522-182">Bir RCL 'ye yeni bir varlık eklendiğinde, bir uygulamanın yeni varlığa erişebilmesi için bildirim güncellemek üzere RCL 'nin yeniden oluşturulması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2d522-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="2d522-183">Yayımlama</span><span class="sxs-lookup"><span data-stu-id="2d522-183">Publish</span></span>

<span data-ttu-id="2d522-184">Uygulama yayımlandığında, tüm başvurulan projeler ve paketlerin yardımcı varlıkları altında `_content/{LIBRARY NAME}/`yayımlanan uygulamanın *Wwwroot* klasörüne kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="2d522-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2d522-185">Razor görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri](xref:blazor/class-libraries), [Görünüm bileşenleri](xref:mvc/views/view-components)ve veri modelleri Razor sınıf kitaplığı 'nda (RCL) yerleşik olarak bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="2d522-186">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="2d522-187">Uygulamalar RCL 'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="2d522-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="2d522-188">Hem Web uygulamasında hem de RCL 'de bir görünüm, kısmi görünüm veya Razor sayfası bulunduğunda, Web uygulamasındaki Razor biçimlendirmesi (*. cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="2d522-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="2d522-189">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d522-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="2d522-190">Razor Kullanıcı arabirimi içeren bir sınıf kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2d522-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d522-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d522-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2d522-192">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2d522-193">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2d522-194">Kitaplığı adlandırın (örneğin, "RazorClassLib") > **Tamam**.</span><span class="sxs-lookup"><span data-stu-id="2d522-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="2d522-195">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.</span><span class="sxs-lookup"><span data-stu-id="2d522-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="2d522-196">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2d522-197">**Razor sınıfı kitaplığı** > **Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="2d522-198">RCL aşağıdaki proje dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="2d522-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2d522-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d522-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d522-200">Komut satırından komutunu çalıştırın `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="2d522-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="2d522-201">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2d522-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="2d522-202">Daha fazla bilgi için bkz. [DotNet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="2d522-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="2d522-203">Oluşturulan görünüm kitaplığıyla bir dosya adı çarpışmasını önlemek için, kitaplık adının bitmediğinden emin olun `.Views`.</span><span class="sxs-lookup"><span data-stu-id="2d522-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="2d522-204">RCL 'ye Razor dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="2d522-205">ASP.NET Core şablonları RCL içeriğinin *Areas* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="2d522-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="2d522-206">' De `~/Pages` içeriğini kullanıma sunan bir RCL oluşturmak Için [RCL Pages düzenine](#rcl-pages-layout) bakın. `~/Areas/Pages`</span><span class="sxs-lookup"><span data-stu-id="2d522-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="2d522-207">RCL içeriğine başvur</span><span class="sxs-lookup"><span data-stu-id="2d522-207">Reference RCL content</span></span>

<span data-ttu-id="2d522-208">RCL 'ye şu şekilde başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="2d522-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="2d522-209">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="2d522-209">NuGet package.</span></span> <span data-ttu-id="2d522-210">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [DotNet paket ekleme](/dotnet/core/tools/dotnet-add-package) ve [bir NuGet paketi oluşturma ve yayımlama](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="2d522-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="2d522-211">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="2d522-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="2d522-212">Bkz. [DotNet-başvuru Ekle](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="2d522-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="2d522-213">İzlenecek yol: bir Razor Pages projesinden bir RCL projesi oluşturma ve kullanma</span><span class="sxs-lookup"><span data-stu-id="2d522-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="2d522-214">[Tüm projeyi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) indirebilir ve oluşturmak yerine test edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2d522-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="2d522-215">Örnek indirme, projenin test olmasını kolaylaştıran ek kod ve bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="2d522-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="2d522-216">Yükleme örnekleri ve adım adım yönergeler hakkındaki açıklamalarınızla [Bu GitHub sorunuyla](https://github.com/dotnet/AspNetCore.Docs/issues/6098) ilgili geri bildirimde bulunun.</span><span class="sxs-lookup"><span data-stu-id="2d522-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="2d522-217">İndirme uygulamasını test etme</span><span class="sxs-lookup"><span data-stu-id="2d522-217">Test the download app</span></span>

<span data-ttu-id="2d522-218">Tamamlanmış uygulamayı indirmediyseniz ve gözden geçirme projesi oluşturmak istiyorsanız, [sonraki bölüme](#create-an-rcl)atlayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d522-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d522-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2d522-220">Visual Studio 'da *. sln* dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="2d522-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="2d522-221">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d522-222">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d522-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d522-223">*CLI* dizinindeki bir komut isteminden RCL ve Web uygulamasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2d522-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="2d522-224">*WebApp1* dizinine gidin ve uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2d522-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="2d522-225">[Test WebApp1](#test-webapp1) içindeki yönergeleri izleyin</span><span class="sxs-lookup"><span data-stu-id="2d522-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="2d522-226">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="2d522-226">Create an RCL</span></span>

<span data-ttu-id="2d522-227">Bu bölümde bir RCL oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2d522-227">In this section, an RCL is created.</span></span> <span data-ttu-id="2d522-228">Razor dosyaları RCL 'ye eklenir.</span><span class="sxs-lookup"><span data-stu-id="2d522-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d522-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d522-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2d522-230">RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-230">Create the RCL project:</span></span>

* <span data-ttu-id="2d522-231">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2d522-232">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2d522-233">Uygulamayı **RazorUIClassLib** > **Tamam**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="2d522-234">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2d522-235">**Razor sınıfı kitaplığı** > **Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="2d522-236">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml*adlı bir Razor kısmi görünüm dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d522-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d522-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d522-238">Komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2d522-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="2d522-239">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="2d522-239">The preceding commands:</span></span>

* <span data-ttu-id="2d522-240">`RazorUIClassLib` RCL 'yi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2d522-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="2d522-241">Razor _Message sayfası oluşturur ve RCL 'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="2d522-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="2d522-242">`-np` Parametresi, sayfasını bir `PageModel`olmadan oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2d522-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="2d522-243">Bir [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL 'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="2d522-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="2d522-244">*_ViewStart. cshtml* dosyası, Razor Pages projenin (bir sonraki bölüme eklenen) düzeninin yerleşimini kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2d522-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="2d522-245">Projeye Razor dosyaları ve klasörleri ekleme</span><span class="sxs-lookup"><span data-stu-id="2d522-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="2d522-246">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2d522-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="2d522-247">*RazorUIClassLib/Areas/MyFeature/Pages/Sayfa1. cshtml* içindeki biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="2d522-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="2d522-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`kısmi görünümü kullanmak için gerekir (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="2d522-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="2d522-249">`@addTagHelper` Yönergesini eklemek yerine bir *_ViewImports. cshtml* dosyası ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2d522-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="2d522-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2d522-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="2d522-251">*_ViewImports. cshtml*hakkında daha fazla bilgi için bkz. [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="2d522-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="2d522-252">Derleyici hatası olmadığını doğrulamak için sınıf kitaplığı oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="2d522-253">Derleme çıkışı *RazorUIClassLib. dll* ve *RazorUIClassLib. views. dll*içerir.</span><span class="sxs-lookup"><span data-stu-id="2d522-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="2d522-254">*RazorUIClassLib. views. dll* derlenen Razor içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="2d522-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="2d522-255">Razor Pages projesinden Razor Kullanıcı arabirimi kitaplığını kullanma</span><span class="sxs-lookup"><span data-stu-id="2d522-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d522-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d522-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2d522-257">Razor Pages Web uygulaması oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="2d522-258">**Çözüm Gezgini**, **Yeni proje** **Ekle** >> çözüme sağ tıklayın.  </span><span class="sxs-lookup"><span data-stu-id="2d522-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="2d522-259">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2d522-260">Uygulamayı **WebApp1**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="2d522-261">**ASP.NET Core 2,1** veya sonraki bir sürümü seçildiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="2d522-262">**Web uygulaması** > **Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="2d522-263">**Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **Başlangıç projesi olarak ayarla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="2d522-264">**Çözüm Gezgini**, **WebApp1** ' ye sağ tıklayın ve **yapı bağımlılıkları** > **Proje bağımlılıkları**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="2d522-265">**WebApp1**'in bağımlılığı olarak **RazorUIClassLib** denetleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="2d522-266">**Çözüm Gezgini**, **WebApp1** ' a sağ tıklayın ve **başvuru** **Ekle** > ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2d522-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="2d522-267">**Başvuru Yöneticisi** iletişim kutusunda **RazorUIClassLib** > **Tamam**' ı işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="2d522-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="2d522-268">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2d522-269">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2d522-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d522-270">Razor Pages uygulamasını ve RCL 'yi içeren bir Razor Pages Web uygulaması ve çözüm dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="2d522-271">Web uygulamasını derleyin ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2d522-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="2d522-272">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="2d522-272">Test WebApp1</span></span>

<span data-ttu-id="2d522-273">UI sınıf `/MyFeature/Page1` kitaplığının kullanımda olduğunu doğrulamak için öğesine gidin. Razor</span><span class="sxs-lookup"><span data-stu-id="2d522-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="2d522-274">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kıl</span><span class="sxs-lookup"><span data-stu-id="2d522-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="2d522-275">Hem Web uygulamasında hem de RCL 'de Razor bir görünüm, kısmi görünüm veya sayfa bulunduğunda, Web uygulamasındaki Razor biçimlendirme (*. cshtml* dosyası) öncelik kazanır.</span><span class="sxs-lookup"><span data-stu-id="2d522-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="2d522-276">Örneğin, *WebApp1/Areas/MyFeature/Pages/Sayfa1. cshtml* öğesini WebApp1 öğesine ekleyin ve WebApp1 içindeki Sayfa1, RCL 'deki Sayfa1 'e göre öncelikli olur.</span><span class="sxs-lookup"><span data-stu-id="2d522-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="2d522-277">Örnek indirme içinde *WebApp1/Areas/MyFeature2* öğesini *WebApp1/Areas/myfeature* olarak yeniden adlandırın ve test önceliğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="2d522-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="2d522-278">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message. cshtml* kısmi görünümünü *WebApp1/Areas/Myfeature/Pages/Shared/_Message. cshtml*'ye kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="2d522-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="2d522-279">Biçimlendirmeyi yeni konumu belirtecek şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2d522-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="2d522-280">Uygulamanın kısmi sürümünün kullanılmakta olduğunu doğrulamak için uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2d522-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="2d522-281">RCL sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="2d522-281">RCL Pages layout</span></span>

<span data-ttu-id="2d522-282">RCL içeriğine, Web uygulamasının *Sayfalar* klasörünün bir parçası olmasına rağmen başvurmak için, aşağıdaki dosya yapısıyla RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2d522-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="2d522-283">*RazorUIClassLib/sayfalar*</span><span class="sxs-lookup"><span data-stu-id="2d522-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="2d522-284">*RazorUIClassLib/sayfalar/paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="2d522-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="2d522-285">*RazorUIClassLib/Pages/Shared* iki kısmi dosya içerir: *_Header. cshtml* ve *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2d522-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="2d522-286">`<partial>` Etiketler *_Layout. cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="2d522-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2d522-287">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2d522-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
