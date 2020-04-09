---
title: dotnet aspnet-codegenerator komutu
author: rick-anderson
description: Dotnet aspnet-codegenerator komut iskeleleri ASP.NET Core projeleri.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665188"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="87e5c-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="87e5c-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="87e5c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="87e5c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="87e5c-105">`dotnet aspnet-codegenerator`- ASP.NET Core iskele motoru çalışır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="87e5c-106">`dotnet aspnet-codegenerator`sadece komut satırından iskele için gerekli olan, Visual Studio ile iskele kullanmak için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="87e5c-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="87e5c-107">Bu makale [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve sonrası için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="87e5c-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="87e5c-108">Aspnet kodlayıcı yükleme</span><span class="sxs-lookup"><span data-stu-id="87e5c-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="87e5c-109">`dotnet-aspnet-codegenerator`yüklenmesi gereken genel bir [araçtır.](/dotnet/core/tools/global-tools)</span><span class="sxs-lookup"><span data-stu-id="87e5c-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="87e5c-110">Aşağıdaki komut `dotnet-aspnet-codegenerator` aracın en son kararlı sürümünü yükler:</span><span class="sxs-lookup"><span data-stu-id="87e5c-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="87e5c-111">Yüklenen .NET `dotnet-aspnet-codegenerator` Core SDK'lardan edinilebilen en son kararlı sürüme aşağıdaki komut güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="87e5c-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="87e5c-112">Özet</span><span class="sxs-lookup"><span data-stu-id="87e5c-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="87e5c-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87e5c-113">Description</span></span>

<span data-ttu-id="87e5c-114">Küresel `dotnet aspnet-codegenerator` komut ASP.NET Core kod jeneratörve iskele motoru çalışır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="87e5c-115">Bağımsız Değişkenler</span><span class="sxs-lookup"><span data-stu-id="87e5c-115">Arguments</span></span>

`generator`

<span data-ttu-id="87e5c-116">Çalışacak kod jeneratörü.</span><span class="sxs-lookup"><span data-stu-id="87e5c-116">The code generator to run.</span></span> <span data-ttu-id="87e5c-117">Aşağıdaki jeneratörler mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="87e5c-117">The following generators are available:</span></span>

| <span data-ttu-id="87e5c-118">Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="87e5c-118">Generator</span></span> | <span data-ttu-id="87e5c-119">İşlem</span><span class="sxs-lookup"><span data-stu-id="87e5c-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="87e5c-120">alan</span><span class="sxs-lookup"><span data-stu-id="87e5c-120">area</span></span>      | [<span data-ttu-id="87e5c-121">İskeleler Bir Alan</span><span class="sxs-lookup"><span data-stu-id="87e5c-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="87e5c-122">denetleyici</span><span class="sxs-lookup"><span data-stu-id="87e5c-122">controller</span></span>| [<span data-ttu-id="87e5c-123">İskele bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="87e5c-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="87e5c-124">identity</span><span class="sxs-lookup"><span data-stu-id="87e5c-124">identity</span></span>  | [<span data-ttu-id="87e5c-125">İskele kimliği</span><span class="sxs-lookup"><span data-stu-id="87e5c-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="87e5c-126">jilet sayfası</span><span class="sxs-lookup"><span data-stu-id="87e5c-126">razorpage</span></span> | [<span data-ttu-id="87e5c-127">İskeleler Jilet Sayfaları</span><span class="sxs-lookup"><span data-stu-id="87e5c-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="87e5c-128">görüntüle</span><span class="sxs-lookup"><span data-stu-id="87e5c-128">view</span></span>      | [<span data-ttu-id="87e5c-129">İskeleler bir görünüm</span><span class="sxs-lookup"><span data-stu-id="87e5c-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="87e5c-130">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="87e5c-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="87e5c-131">NuGet paket dizinini belirtir.</span><span class="sxs-lookup"><span data-stu-id="87e5c-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="87e5c-132">Yapı yapılandırmasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="87e5c-132">Defines the build configuration.</span></span> <span data-ttu-id="87e5c-133">Varsayılan değer: `Debug`.</span><span class="sxs-lookup"><span data-stu-id="87e5c-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="87e5c-134">Kullanılacak [Hedef Çerçeve.](/dotnet/standard/frameworks)</span><span class="sxs-lookup"><span data-stu-id="87e5c-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="87e5c-135">Örneğin, `net46`.</span><span class="sxs-lookup"><span data-stu-id="87e5c-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="87e5c-136">Yapı tabanı yolu.</span><span class="sxs-lookup"><span data-stu-id="87e5c-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="87e5c-137">Komut için kısa bir yardım yazdırır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="87e5c-138">Çalıştırmadan önce projeyi oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="87e5c-138">Doesn't build the project before running.</span></span> <span data-ttu-id="87e5c-139">Ayrıca bayrağı da `--no-restore` örtülü olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="87e5c-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="87e5c-140">Proje dosyasının çalışma yolunu (klasör adı veya tam yol) belirtir.</span><span class="sxs-lookup"><span data-stu-id="87e5c-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="87e5c-141">Belirtilmemişse, varsayılan olarak geçerli dizine göre olur.</span><span class="sxs-lookup"><span data-stu-id="87e5c-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="87e5c-142">Jeneratör seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87e5c-142">Generator options</span></span>

<span data-ttu-id="87e5c-143">Aşağıdaki bölümler, desteklenen jeneratörler için mevcut seçenekleri ayrıntılı olarak açıklanır:</span><span class="sxs-lookup"><span data-stu-id="87e5c-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="87e5c-144">Alan</span><span class="sxs-lookup"><span data-stu-id="87e5c-144">Area</span></span>
* <span data-ttu-id="87e5c-145">Denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="87e5c-145">Controller</span></span>
* <span data-ttu-id="87e5c-146">Kimlik</span><span class="sxs-lookup"><span data-stu-id="87e5c-146">Identity</span></span>  
* <span data-ttu-id="87e5c-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="87e5c-147">Razorpage</span></span>
* <span data-ttu-id="87e5c-148">Görüntüle</span><span class="sxs-lookup"><span data-stu-id="87e5c-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="87e5c-149">Alan seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87e5c-149">Area options</span></span>

<span data-ttu-id="87e5c-150">Bu araç, denetleyicileri ve görünümleri ile core web projeleri ASP.NET için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="87e5c-151">Razor Pages uygulamaları için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="87e5c-152">Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="87e5c-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="87e5c-153">Önceki komut aşağıdaki klasörleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="87e5c-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="87e5c-154">*Alanlar*</span><span class="sxs-lookup"><span data-stu-id="87e5c-154">*Areas*</span></span>
  * <span data-ttu-id="87e5c-155">*AlanNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="87e5c-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="87e5c-156">*Denetleyiciler*</span><span class="sxs-lookup"><span data-stu-id="87e5c-156">*Controllers*</span></span>
    * <span data-ttu-id="87e5c-157">*Veri*</span><span class="sxs-lookup"><span data-stu-id="87e5c-157">*Data*</span></span>
    * <span data-ttu-id="87e5c-158">*Modeller*</span><span class="sxs-lookup"><span data-stu-id="87e5c-158">*Models*</span></span>
    * <span data-ttu-id="87e5c-159">*Görünümler*</span><span class="sxs-lookup"><span data-stu-id="87e5c-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="87e5c-160">Denetleyici seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87e5c-160">Controller options</span></span>

<span data-ttu-id="87e5c-161">Aşağıdaki tablo da `aspnet-codegenerator` `controller` seçenekler `razorpage`ve:</span><span class="sxs-lookup"><span data-stu-id="87e5c-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="87e5c-162">Aşağıdaki tabloda benzersiz `aspnet-codegenerator controller`seçenekler listeledir:</span><span class="sxs-lookup"><span data-stu-id="87e5c-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="87e5c-163">Seçenek</span><span class="sxs-lookup"><span data-stu-id="87e5c-163">Option</span></span>               | <span data-ttu-id="87e5c-164">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87e5c-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="87e5c-165">--controllerName veya -name</span><span class="sxs-lookup"><span data-stu-id="87e5c-165">--controllerName or -name</span></span> | <span data-ttu-id="87e5c-166">Denetleyicinin adı.</span><span class="sxs-lookup"><span data-stu-id="87e5c-166">Name of the controller.</span></span> |
| <span data-ttu-id="87e5c-167">--useAsyncActions veya -async</span><span class="sxs-lookup"><span data-stu-id="87e5c-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="87e5c-168">Async denetleyici eylemleri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87e5c-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="87e5c-169">--noViews veya -nv</span><span class="sxs-lookup"><span data-stu-id="87e5c-169">--noViews or -nv</span></span> | <span data-ttu-id="87e5c-170">Görünüm **oluşturmayın.**</span><span class="sxs-lookup"><span data-stu-id="87e5c-170">Generate **no** views.</span></span> |
| <span data-ttu-id="87e5c-171">--restWithNoViews veya -api</span><span class="sxs-lookup"><span data-stu-id="87e5c-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="87e5c-172">REST stili API'li bir Denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87e5c-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="87e5c-173">`noViews`varsayılabilir ve görünümle ilgili seçenekler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="87e5c-174">--readWriteActions veya -actions</span><span class="sxs-lookup"><span data-stu-id="87e5c-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="87e5c-175">Model olmadan okuma/yazma eylemleri içeren denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87e5c-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="87e5c-176">`aspnet-codegenerator controller` Komut üzerinde `-h` yardım için anahtarı kullanın:</span><span class="sxs-lookup"><span data-stu-id="87e5c-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="87e5c-177">Örnek olarak [film modelini İskele'ye](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator controller`bakın.</span><span class="sxs-lookup"><span data-stu-id="87e5c-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="87e5c-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="87e5c-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="87e5c-179">Jilet Sayfaları, yeni sayfanın adını ve kullanılacak şablonu belirterek ayrı ayrı iskeleye eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="87e5c-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="87e5c-180">Desteklenen şablonlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="87e5c-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="87e5c-181">Örneğin, aşağıdaki komut *MyEdit.cshtml* oluşturmak için Edit şablonunu kullanır ve *MyEdit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="87e5c-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="87e5c-182">Genellikle, şablon ve oluşturulan dosya adı belirtilmemiş ve aşağıdaki şablonlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="87e5c-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="87e5c-183">Aşağıdaki tablo da `aspnet-codegenerator` `razorpage` seçenekler `controller`ve:</span><span class="sxs-lookup"><span data-stu-id="87e5c-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="87e5c-184">Aşağıdaki tabloda benzersiz `aspnet-codegenerator razorpage`seçenekler listeledir:</span><span class="sxs-lookup"><span data-stu-id="87e5c-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="87e5c-185">Seçenek</span><span class="sxs-lookup"><span data-stu-id="87e5c-185">Option</span></span>               | <span data-ttu-id="87e5c-186">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87e5c-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="87e5c-187">--namespaceName veya -namespace</span><span class="sxs-lookup"><span data-stu-id="87e5c-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="87e5c-188">Oluşturulan PageModel için kullanılacak ad alanının adı</span><span class="sxs-lookup"><span data-stu-id="87e5c-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="87e5c-189">--partialView veya -kısmi</span><span class="sxs-lookup"><span data-stu-id="87e5c-189">--partialView or -partial</span></span> | <span data-ttu-id="87e5c-190">Kısmi bir görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87e5c-190">Generate a partial view.</span></span> <span data-ttu-id="87e5c-191">Bu belirtilirse düzen seçenekleri -l ve -udl yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="87e5c-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="87e5c-192">--noPageModel veya -npm</span><span class="sxs-lookup"><span data-stu-id="87e5c-192">--noPageModel or -npm</span></span> | <span data-ttu-id="87e5c-193">Boş şablon için PageModel sınıfı oluşturmamak için geçiş yap</span><span class="sxs-lookup"><span data-stu-id="87e5c-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="87e5c-194">`aspnet-codegenerator razorpage` Komut üzerinde `-h` yardım için anahtarı kullanın:</span><span class="sxs-lookup"><span data-stu-id="87e5c-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="87e5c-195">Örnek olarak [film modelini İskele'ye](/aspnet/core/tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage`bakın.</span><span class="sxs-lookup"><span data-stu-id="87e5c-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="87e5c-196">Kimlik</span><span class="sxs-lookup"><span data-stu-id="87e5c-196">Identity</span></span>

<span data-ttu-id="87e5c-197">Bkz. [İskele Kimliği](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="87e5c-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
