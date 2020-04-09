---
title: ASP.NET Core ile sınıf kütüphanelerinde yeniden kullanılabilir Razor UI
author: Rick-Anderson
description: ASP.NET Core'daki bir sınıf kitaplığında kısmi görünümler kullanarak yeniden kullanılabilir Razor UI'nin nasıl oluşturulabildiğini açıklar.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667568"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="31d02-103">ASP.NET Core'daki Razor sınıfı kitaplık projesini kullanarak yeniden kullanılabilir ui oluşturma</span><span class="sxs-lookup"><span data-stu-id="31d02-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="31d02-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="31d02-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31d02-105">Jilet görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri,](xref:blazor/class-libraries) [Bileşenleri görüntüle](xref:mvc/views/view-components)ve veri modelleri Bir Razor sınıfı kitaplık (RCL) içine inşa edilebilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="31d02-106">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="31d02-107">Uygulamalar RCL'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="31d02-108">Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="31d02-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="31d02-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31d02-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="31d02-110">Razor UI içeren bir sınıf kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="31d02-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31d02-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31d02-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31d02-112">Visual Studio'dan **yeni bir proje oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="31d02-113">**Sonraki** **Razor Class Kitaplığı'nı** > seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="31d02-114">Kitaplığı adlandırın (örneğin, "RazorClassLib"), > **Oluştur.**</span><span class="sxs-lookup"><span data-stu-id="31d02-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="31d02-115">Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="31d02-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="31d02-116">Görünümleri desteklemeniz gerekiyorsa **Destek sayfalarını ve görünümlerini** seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="31d02-117">Varsayılan olarak, yalnızca Razor Pages desteklenir.</span><span class="sxs-lookup"><span data-stu-id="31d02-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="31d02-118">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-118">Select **Create**.</span></span>

<span data-ttu-id="31d02-119">Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır.</span><span class="sxs-lookup"><span data-stu-id="31d02-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="31d02-120">**Destek sayfaları ve görünümleri** seçeneği sayfaları ve görünümleri destekler.</span><span class="sxs-lookup"><span data-stu-id="31d02-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31d02-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="31d02-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31d02-122">Komut satırından çalıştırın. `dotnet new razorclasslib`</span><span class="sxs-lookup"><span data-stu-id="31d02-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="31d02-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="31d02-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="31d02-124">Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır.</span><span class="sxs-lookup"><span data-stu-id="31d02-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="31d02-125">Sayfalar `--support-pages-and-views` ve`dotnet new razorclasslib --support-pages-and-views`görünümler için destek sağlamak için seçeneği ni geçirin .</span><span class="sxs-lookup"><span data-stu-id="31d02-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="31d02-126">Daha fazla bilgi için [dotnet yeni.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="31d02-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="31d02-127">Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="31d02-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="31d02-128">RCL'ye Razor dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="31d02-129">ASP.NET Core şablonları RCL içeriğinin *Alanlar* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="31d02-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="31d02-130">İçeriği `~/Pages` yerine yerine ortaya çıkaran bir RCL oluşturmak `~/Areas/Pages`için [RCL Sayfaları düzenine](#rcl-pages-layout) bakın.</span><span class="sxs-lookup"><span data-stu-id="31d02-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="31d02-131">Referans RCL içeriği</span><span class="sxs-lookup"><span data-stu-id="31d02-131">Reference RCL content</span></span>

<span data-ttu-id="31d02-132">RCL tarafından başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="31d02-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="31d02-133">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="31d02-133">NuGet package.</span></span> <span data-ttu-id="31d02-134">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [dotnet paketi ekleyin](/dotnet/core/tools/dotnet-add-package) ve bir [NuGet paketi oluşturun ve yayımlayın.](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)</span><span class="sxs-lookup"><span data-stu-id="31d02-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="31d02-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="31d02-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="31d02-136">[Bkz. dotnet-add başvurusu.](/dotnet/core/tools/dotnet-add-reference)</span><span class="sxs-lookup"><span data-stu-id="31d02-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="31d02-137">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="31d02-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="31d02-138">Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="31d02-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="31d02-139">Örneğin, *WebApp1'e WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ekleyin ve WebApp1'deki Page1, RCL'deki Page1'den önce gelir.</span><span class="sxs-lookup"><span data-stu-id="31d02-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="31d02-140">Örnek indirmede, önceliği test etmek için *WebApp1/Areas/MyFeature2'yi* *WebApp1/Areas/MyFeature* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="31d02-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünümü *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*adresine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="31d02-142">Yeni konumu belirtmek için biçimlendirmeyi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="31d02-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="31d02-143">Uygulamanın kısmi sürümünün kullanıldığını doğrulamak için uygulamayı oluşturun ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="31d02-144">RCL Sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="31d02-144">RCL Pages layout</span></span>

<span data-ttu-id="31d02-145">RCL içeriğine web uygulamasının *Sayfalar* klasörünün bir parçasıymış gibi başvurmak için, aşağıdaki dosya yapısına sahip RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="31d02-146">*RazoruiClassLib/Sayfalar*</span><span class="sxs-lookup"><span data-stu-id="31d02-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="31d02-147">*RazoruiClassLib/Sayfalar/Paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="31d02-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="31d02-148">*RazorUIClassLib/Pages/Shared'in* iki kısmi dosya içerdiğini varsayalım: *_Header.cshtml* ve *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="31d02-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="31d02-149">Etiketler `<partial>` *_Layout.cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="31d02-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="31d02-150">Statik varlıklara sahip bir RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="31d02-150">Create an RCL with static assets</span></span>

<span data-ttu-id="31d02-151">Bir RCL, RCL veya RCL'nin tüketen uygulaması tarafından başvurulan eşlik eden statik varlıklar gerektirebilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="31d02-152">ASP.NET Core, tüketen bir uygulamanın kullanabileceği statik varlıkları içeren RCL'ler oluşturmaya olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="31d02-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="31d02-153">Bir RCL'nin parçası olarak eşlik eden varlıkları eklemek için, sınıf kitaplığında bir *wwwroot* klasörü oluşturun ve bu klasöre gerekli dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="31d02-154">Bir RCL paketlenirken, *wwwroot* klasöründeki tüm tamamlayıcı varlıklar otomatik olarak pakete dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="31d02-155">Statik varlıkları hariç tutma</span><span class="sxs-lookup"><span data-stu-id="31d02-155">Exclude static assets</span></span>

<span data-ttu-id="31d02-156">Statik kıymetleri hariç tutmak için, `$(DefaultItemExcludes)` proje dosyasındaki özellik grubuna istenen dışlama yolunu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="31d02-157">Bir semicolon ile`;`ayrı girişleri ( ).</span><span class="sxs-lookup"><span data-stu-id="31d02-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="31d02-158">Aşağıdaki örnekte, *wwwroot* klasöründeki *lib.css* stylesheet statik bir varlık olarak kabul etmez ve yayınlanan RCL'ye dahil değildir:</span><span class="sxs-lookup"><span data-stu-id="31d02-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="31d02-159">Typescript tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="31d02-159">Typescript integration</span></span>

<span data-ttu-id="31d02-160">TypeScript dosyalarını Bir RCL'ye eklemek için:</span><span class="sxs-lookup"><span data-stu-id="31d02-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="31d02-161">TypeScript dosyalarını (*.ts)* *wwwroot* klasörünün dışına yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="31d02-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="31d02-162">Örneğin, dosyaları *Istemci* klasörüne yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="31d02-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="31d02-163">*Wwwroot* klasörü için TypeScript yapı çıktısını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="31d02-164">Proje `TypescriptOutDir` dosyasındaki bir `PropertyGroup` özelliğin içindeki özelliği ayarlama:</span><span class="sxs-lookup"><span data-stu-id="31d02-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="31d02-165">Project dosyasına aşağıdaki hedefi ekleyerek, `ResolveCurrentProjectStaticWebAssets` hedefin bağımlılığı olarak TypeScript hedefini `PropertyGroup` ekleyin:</span><span class="sxs-lookup"><span data-stu-id="31d02-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="31d02-166">Başvurulan bir RCL'den içerik tüketin</span><span class="sxs-lookup"><span data-stu-id="31d02-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="31d02-167">RCL'nin *wwwroot* klasöründe yer alan dosyalar, önek `_content/{LIBRARY NAME}/`altında RCL'ye veya tüketen uygulamaya maruz kalır.</span><span class="sxs-lookup"><span data-stu-id="31d02-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="31d02-168">Örneğin, *Razor.Class.Lib* adlı bir kitaplık, 'de `_content/Razor.Class.Lib/`statik içeriğe giden bir yol ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="31d02-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="31d02-169">NuGet paketi üretirken ve montaj adı paket kimliğiyle aynı değildir, paket `{LIBRARY NAME}`kimliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="31d02-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="31d02-170">Tüketen uygulama, kitaplık tarafından sağlanan `<script>` `<style>`statik `<img>`varlıklara , , ve diğer HTML etiketleri ile başvurur.</span><span class="sxs-lookup"><span data-stu-id="31d02-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="31d02-171">Tüketen uygulamanın [aşağıdaki](xref:fundamentals/static-files) durumlarda `Startup.Configure`statik dosya desteği etkinleştirilmiş olması gerekir:</span><span class="sxs-lookup"><span data-stu-id="31d02-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="31d02-172">Alıcı uygulamayı yapı çıktısından çalıştırırken (`dotnet run`), statik web varlıkları Geliştirme ortamında varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="31d02-173">Yapı çıktısından kaçarken diğer ortamlardaki `UseStaticWebAssets` varlıkları desteklemek *için, Program.cs*ev sahibi oluşturucuyu arayın:</span><span class="sxs-lookup"><span data-stu-id="31d02-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="31d02-174">Yayınlanmış `UseStaticWebAssets` çıktıdan bir uygulamayı çalıştırırken arama`dotnet publish`yapılması gerekmez ( ).</span><span class="sxs-lookup"><span data-stu-id="31d02-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="31d02-175">Çok projeli geliştirme akışı</span><span class="sxs-lookup"><span data-stu-id="31d02-175">Multi-project development flow</span></span>

<span data-ttu-id="31d02-176">Tüketen uygulama çalıştığında:</span><span class="sxs-lookup"><span data-stu-id="31d02-176">When the consuming app runs:</span></span>

* <span data-ttu-id="31d02-177">RCL'deki varlıklar özgün klasörlerinde kalır.</span><span class="sxs-lookup"><span data-stu-id="31d02-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="31d02-178">Varlıklar tüketen uygulamaya taşınmaz.</span><span class="sxs-lookup"><span data-stu-id="31d02-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="31d02-179">RCL'nin *wwwroot* klasöründeki herhangi bir değişiklik, RCL yeniden inşa edildikten ve tüketen uygulamayı yeniden oluşturmadan tüketen uygulamaya yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="31d02-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="31d02-180">RCL oluşturulduğunda, statik web varlık konumlarını açıklayan bir bildirim oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="31d02-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="31d02-181">Tüketen uygulama, başvurulan projeler den ve paketlerden varlıkları tüketmek için çalışma zamanında manifestoyu okur.</span><span class="sxs-lookup"><span data-stu-id="31d02-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="31d02-182">Bir RCL'ye yeni bir varlık eklendiğinde, tüketen bir uygulamanın yeni varlığa erişemeden önce bildirimini güncelleştirmek için RCL'nin yeniden inşa edilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="31d02-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="31d02-183">Yayımlama</span><span class="sxs-lookup"><span data-stu-id="31d02-183">Publish</span></span>

<span data-ttu-id="31d02-184">Uygulama yayımlandığında, başvurulan tüm proje ve paketlerdeki yardımcı varlıklar, altında `_content/{LIBRARY NAME}/`yayınlanan uygulamanın *wwwroot* klasörüne kopyalanır.</span><span class="sxs-lookup"><span data-stu-id="31d02-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31d02-185">Jilet görünümleri, sayfalar, denetleyiciler, sayfa modelleri, [Razor bileşenleri,](xref:blazor/class-libraries) [Bileşenleri görüntüle](xref:mvc/views/view-components)ve veri modelleri Bir Razor sınıfı kitaplık (RCL) içine inşa edilebilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="31d02-186">RCL paketlenebilir ve yeniden kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="31d02-187">Uygulamalar RCL'yi içerebilir ve içerdiği görünümleri ve sayfaları geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="31d02-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="31d02-188">Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="31d02-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="31d02-189">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31d02-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="31d02-190">Razor UI içeren bir sınıf kitaplığı oluşturma</span><span class="sxs-lookup"><span data-stu-id="31d02-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31d02-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31d02-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="31d02-192">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="31d02-193">**Core Web Uygulaması ASP.NET**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="31d02-194">Kitaplığı adlandırın (örneğin, "RazorClassLib") **> Tamam.**</span><span class="sxs-lookup"><span data-stu-id="31d02-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="31d02-195">Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="31d02-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="31d02-196">**Core 2.1** veya daha sonra ASP.NET doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="31d02-197">**Razor Class Kitaplığı** > **Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="31d02-198">Bir RCL aşağıdaki proje dosyasına sahiptir:</span><span class="sxs-lookup"><span data-stu-id="31d02-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="31d02-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="31d02-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31d02-200">Komut satırından çalıştırın. `dotnet new razorclasslib`</span><span class="sxs-lookup"><span data-stu-id="31d02-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="31d02-201">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="31d02-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="31d02-202">Daha fazla bilgi için [dotnet yeni.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="31d02-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="31d02-203">Oluşturulan görünüm kitaplığıyla dosya adı çakışmasını önlemek için kitaplık adının `.Views`'de bitmediğinden emin olun</span><span class="sxs-lookup"><span data-stu-id="31d02-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="31d02-204">RCL'ye Razor dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="31d02-205">ASP.NET Core şablonları RCL içeriğinin *Alanlar* klasöründe olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="31d02-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="31d02-206">İçeriği `~/Pages` yerine yerine ortaya çıkaran bir RCL oluşturmak `~/Areas/Pages`için [RCL Sayfaları düzenine](#rcl-pages-layout) bakın.</span><span class="sxs-lookup"><span data-stu-id="31d02-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="31d02-207">Referans RCL içeriği</span><span class="sxs-lookup"><span data-stu-id="31d02-207">Reference RCL content</span></span>

<span data-ttu-id="31d02-208">RCL tarafından başvurulabilir:</span><span class="sxs-lookup"><span data-stu-id="31d02-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="31d02-209">NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="31d02-209">NuGet package.</span></span> <span data-ttu-id="31d02-210">Bkz. [NuGet paketleri oluşturma](/nuget/create-packages/creating-a-package) ve [dotnet paketi ekleyin](/dotnet/core/tools/dotnet-add-package) ve bir [NuGet paketi oluşturun ve yayımlayın.](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)</span><span class="sxs-lookup"><span data-stu-id="31d02-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="31d02-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="31d02-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="31d02-212">[Bkz. dotnet-add başvurusu.](/dotnet/core/tools/dotnet-add-reference)</span><span class="sxs-lookup"><span data-stu-id="31d02-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="31d02-213">Walkthrough: Bir RCL projesi oluşturun ve Bir Razor Pages projesinden kullanın</span><span class="sxs-lookup"><span data-stu-id="31d02-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="31d02-214">[Projenin tamamını](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) indirebilir ve oluşturmak yerine test edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="31d02-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="31d02-215">Örnek karşıdan yükleme, projeyi test etmeyi kolaylaştıran ek kod lar ve bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="31d02-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="31d02-216">[Bu GitHub sorununda,](https://github.com/dotnet/AspNetCore.Docs/issues/6098) indirme örnekleri hakkındaki yorumlarınızla ve adım adım talimatlara karşı geri bildirim bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="31d02-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="31d02-217">İndirme uygulamasını test edin</span><span class="sxs-lookup"><span data-stu-id="31d02-217">Test the download app</span></span>

<span data-ttu-id="31d02-218">Tamamlanan uygulamayı indirmediyseniz ve gözden geçirme projesini oluşturmak yerine bir [sonraki bölüme](#create-an-rcl)atlayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31d02-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31d02-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31d02-220">*.sln* dosyasını Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="31d02-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="31d02-221">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31d02-222">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="31d02-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31d02-223">*Cli* dizinindeki bir komut isteminden, RCL ve web uygulamasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="31d02-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="31d02-224">*WebApp1* dizinine geçin ve uygulamayı çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="31d02-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="31d02-225">[Test WebApp1'deki](#test-webapp1) talimatları izleyin</span><span class="sxs-lookup"><span data-stu-id="31d02-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="31d02-226">RCL oluşturma</span><span class="sxs-lookup"><span data-stu-id="31d02-226">Create an RCL</span></span>

<span data-ttu-id="31d02-227">Bu bölümde bir RCL oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="31d02-227">In this section, an RCL is created.</span></span> <span data-ttu-id="31d02-228">Ustura dosyaları RCL eklenir.</span><span class="sxs-lookup"><span data-stu-id="31d02-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31d02-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31d02-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31d02-230">RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-230">Create the RCL project:</span></span>

* <span data-ttu-id="31d02-231">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="31d02-232">**Core Web Uygulaması ASP.NET**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="31d02-233">Uygulamayı **RazorUIClassLib** > **Tamam**adlandırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="31d02-234">**Core 2.1** veya daha sonra ASP.NET doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="31d02-235">**Razor Class Kitaplığı** > **Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="31d02-236">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*adlı bir Razor kısmi görünüm dosyası ekleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31d02-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="31d02-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31d02-238">Komut satırından aşağıdakileri çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="31d02-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="31d02-239">Önceki komutlar:</span><span class="sxs-lookup"><span data-stu-id="31d02-239">The preceding commands:</span></span>

* <span data-ttu-id="31d02-240">RCL `RazorUIClassLib` oluşturur.</span><span class="sxs-lookup"><span data-stu-id="31d02-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="31d02-241">Bir Jilet _Message sayfası oluşturur ve RCL'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="31d02-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="31d02-242">Parametre `-np` olmadan sayfa oluşturur `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="31d02-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="31d02-243">[bir _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) dosyası oluşturur ve RCL'ye ekler.</span><span class="sxs-lookup"><span data-stu-id="31d02-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="31d02-244">*_ViewStart.cshtml* dosyasının Razor Pages projesinin düzenini kullanması gerekmektedir (bir sonraki bölümde eklenir).</span><span class="sxs-lookup"><span data-stu-id="31d02-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="31d02-245">Projeye Razor dosya ve klasörleri ekleme</span><span class="sxs-lookup"><span data-stu-id="31d02-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="31d02-246">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml'deki* biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="31d02-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="31d02-247">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml'deki* biçimlendirmeyi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="31d02-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="31d02-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`kısmi görünümü kullanmak için`<partial name="_Message" />`gereklidir ( ).</span><span class="sxs-lookup"><span data-stu-id="31d02-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="31d02-249">Yönergeyi `@addTagHelper` eklemek yerine, bir *_ViewImports.cshtml* dosyası ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="31d02-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="31d02-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="31d02-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="31d02-251">*_ViewImports.cshtml*hakkında daha fazla bilgi için [bkz.](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="31d02-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="31d02-252">Derleyici hatası olmadığını doğrulamak için sınıf kitaplığını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="31d02-253">Yapı *çıktısı RazorUIClassLib.dll* ve *RazorUIClassLib.Views.dll*içerir.</span><span class="sxs-lookup"><span data-stu-id="31d02-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="31d02-254">*RazorUIClassLib.Views.dll* derlenmiş Razor içeriğini içerir.</span><span class="sxs-lookup"><span data-stu-id="31d02-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="31d02-255">Razor Pages projesindeki Razor UI kitaplığını kullanma</span><span class="sxs-lookup"><span data-stu-id="31d02-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="31d02-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="31d02-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="31d02-257">Razor Pages web uygulamasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="31d02-258">**Solution Explorer'dan,** Yeni **Proje** **Ekle** >> çözüme sağ tıklayın.  </span><span class="sxs-lookup"><span data-stu-id="31d02-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="31d02-259">**Core Web Uygulaması ASP.NET**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="31d02-260">Uygulamanın adını **WebApp1.**</span><span class="sxs-lookup"><span data-stu-id="31d02-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="31d02-261">**Core 2.1** veya daha sonra ASP.NET doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="31d02-262">**Web Uygulaması Tamam'ı** > **OK**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="31d02-263">**Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **StartUp Project olarak ayarla'yı**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="31d02-264">**Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **Bağımlılıkoluştur** > **Projesi Bağımlılıkları'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="31d02-265">**WebApp1'e**bağımlılık olarak **RazorUIClassLib'i** kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="31d02-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="31d02-266">**Solution Explorer'dan** **WebApp1'e** sağ tıklayın ve **Referans** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="31d02-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="31d02-267">Başvuru **Yöneticisi** iletişim kutusunda, **RazorUIClassLib** > **Tamam'ı**işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="31d02-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="31d02-268">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="31d02-269">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="31d02-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="31d02-270">Razor Pages web uygulaması ve Razor Pages uygulamasını ve RCL'yi içeren bir çözüm dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="31d02-271">Web uygulamasını oluşturun ve çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="31d02-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="31d02-272">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="31d02-272">Test WebApp1</span></span>

<span data-ttu-id="31d02-273">Razor UI sınıf kitaplığının kullanımda olduğunu doğrulamak için `/MyFeature/Page1` göz atın.</span><span class="sxs-lookup"><span data-stu-id="31d02-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="31d02-274">Görünümleri, kısmi görünümleri ve sayfaları geçersiz kılma</span><span class="sxs-lookup"><span data-stu-id="31d02-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="31d02-275">Hem web uygulamasında hem de RCL'de bir görünüm, kısmi görünüm veya Razor Page bulunduğunda, web uygulamasındaki Razor biçimlendirmesi *(.cshtml* dosyası) önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="31d02-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="31d02-276">Örneğin, *WebApp1'e WebApp1/Areas/MyFeature/Pages/Page1.cshtml* ekleyin ve WebApp1'deki Page1, RCL'deki Page1'den önce gelir.</span><span class="sxs-lookup"><span data-stu-id="31d02-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="31d02-277">Örnek indirmede, önceliği test etmek için *WebApp1/Areas/MyFeature2'yi* *WebApp1/Areas/MyFeature* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="31d02-278">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* kısmi görünümü *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*adresine kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="31d02-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="31d02-279">Yeni konumu belirtmek için biçimlendirmeyi güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="31d02-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="31d02-280">Uygulamanın kısmi sürümünün kullanıldığını doğrulamak için uygulamayı oluşturun ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="31d02-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="31d02-281">RCL Sayfaları düzeni</span><span class="sxs-lookup"><span data-stu-id="31d02-281">RCL Pages layout</span></span>

<span data-ttu-id="31d02-282">RCL içeriğine web uygulamasının *Sayfalar* klasörünün bir parçasıymış gibi başvurmak için, aşağıdaki dosya yapısına sahip RCL projesini oluşturun:</span><span class="sxs-lookup"><span data-stu-id="31d02-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="31d02-283">*RazoruiClassLib/Sayfalar*</span><span class="sxs-lookup"><span data-stu-id="31d02-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="31d02-284">*RazoruiClassLib/Sayfalar/Paylaşılan*</span><span class="sxs-lookup"><span data-stu-id="31d02-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="31d02-285">*RazorUIClassLib/Pages/Shared'in* iki kısmi dosya içerdiğini varsayalım: *_Header.cshtml* ve *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="31d02-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="31d02-286">Etiketler `<partial>` *_Layout.cshtml* dosyasına eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="31d02-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="31d02-287">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="31d02-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
