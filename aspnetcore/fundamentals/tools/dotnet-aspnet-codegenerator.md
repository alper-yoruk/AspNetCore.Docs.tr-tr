---
title: DotNet ASPNET-CodeGenerator komutu
author: rick-anderson
description: DotNet ASPNET-CodeGenerator komutu yapı ASP.NET Core projeler.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 88d761b09833a14de5af9f9610753174867aa09a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059994"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="87440-103">DotNet ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="87440-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="87440-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="87440-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="87440-105">`dotnet aspnet-codegenerator` -ASP.NET Core scafkatlama altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="87440-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="87440-106">`dotnet aspnet-codegenerator` yalnızca komut satırından yapı iskelesi sağlamak için gereklidir, Visual Studio ile scafkatlamayı kullanmak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="87440-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="87440-107">Bu makale [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve üzeri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="87440-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="87440-108">ASPNET-CodeGenerator yükleniyor</span><span class="sxs-lookup"><span data-stu-id="87440-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="87440-109">`dotnet-aspnet-codegenerator` yüklenmesi gereken [küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="87440-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="87440-110">Aşağıdaki komut aracın en son kararlı sürümünü yüklüyor `dotnet-aspnet-codegenerator` :</span><span class="sxs-lookup"><span data-stu-id="87440-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="87440-111">Aşağıdaki komut, `dotnet-aspnet-codegenerator` yüklü .NET Core SDK 'larında kullanılabilen en son kararlı sürümü güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="87440-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="87440-112">Özeti</span><span class="sxs-lookup"><span data-stu-id="87440-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="87440-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87440-113">Description</span></span>

<span data-ttu-id="87440-114">`dotnet aspnet-codegenerator`Genel komut ASP.NET Core kod Oluşturucu ve yapı iskelesi altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="87440-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="87440-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="87440-115">Arguments</span></span>

`generator`

<span data-ttu-id="87440-116">Çalıştırılacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="87440-116">The code generator to run.</span></span> <span data-ttu-id="87440-117">Aşağıdaki oluşturucular kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="87440-117">The following generators are available:</span></span>

| <span data-ttu-id="87440-118">Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="87440-118">Generator</span></span>  | <span data-ttu-id="87440-119">İşlem</span><span class="sxs-lookup"><span data-stu-id="87440-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="87440-120">alan</span><span class="sxs-lookup"><span data-stu-id="87440-120">area</span></span>       | [<span data-ttu-id="87440-121">Bir alanı dolandırın</span><span class="sxs-lookup"><span data-stu-id="87440-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="87440-122">denetleyici</span><span class="sxs-lookup"><span data-stu-id="87440-122">controller</span></span> | [<span data-ttu-id="87440-123">Bir denetleyiciyi yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="87440-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="87440-124">identity</span><span class="sxs-lookup"><span data-stu-id="87440-124">identity</span></span>   | [<span data-ttu-id="87440-125">İşleyicilerin Identity</span><span class="sxs-lookup"><span data-stu-id="87440-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="87440-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="87440-126">razorpage</span></span>  | [<span data-ttu-id="87440-127">Yapı iskelesi Razor sayfaları</span><span class="sxs-lookup"><span data-stu-id="87440-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="87440-128">görüntüle</span><span class="sxs-lookup"><span data-stu-id="87440-128">view</span></span>       | [<span data-ttu-id="87440-129">Bir görünümü dolandırın</span><span class="sxs-lookup"><span data-stu-id="87440-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="87440-130">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="87440-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="87440-131">NuGet paket dizinini belirtir.</span><span class="sxs-lookup"><span data-stu-id="87440-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="87440-132">Yapı yapılandırmasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="87440-132">Defines the build configuration.</span></span> <span data-ttu-id="87440-133">`Debug` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="87440-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="87440-134">Kullanılacak hedef [çerçeve](/dotnet/standard/frameworks) .</span><span class="sxs-lookup"><span data-stu-id="87440-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="87440-135">Örneğin, `net46`.</span><span class="sxs-lookup"><span data-stu-id="87440-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="87440-136">Yapı temel yolu.</span><span class="sxs-lookup"><span data-stu-id="87440-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="87440-137">Komut için kısa bir yardım yazdırır.</span><span class="sxs-lookup"><span data-stu-id="87440-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="87440-138">Çalıştırmadan önce projeyi oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="87440-138">Doesn't build the project before running.</span></span> <span data-ttu-id="87440-139">Ayrıca bayrağı örtülü olarak ayarlar `--no-restore` .</span><span class="sxs-lookup"><span data-stu-id="87440-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="87440-140">Çalıştırılacak proje dosyasının yolunu belirtir (klasör adı veya tam yol).</span><span class="sxs-lookup"><span data-stu-id="87440-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="87440-141">Belirtilmezse, varsayılan olarak geçerli dizini alır.</span><span class="sxs-lookup"><span data-stu-id="87440-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="87440-142">Oluşturucu seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87440-142">Generator options</span></span>

<span data-ttu-id="87440-143">Aşağıdaki bölümler, desteklenen oluşturucular için kullanılabilen seçenekleri ayrıntılandırır:</span><span class="sxs-lookup"><span data-stu-id="87440-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="87440-144">Alan</span><span class="sxs-lookup"><span data-stu-id="87440-144">Area</span></span>
* <span data-ttu-id="87440-145">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="87440-145">Controller</span></span>
* Identity  
* <span data-ttu-id="87440-146">Razorsayfasında</span><span class="sxs-lookup"><span data-stu-id="87440-146">Razorpage</span></span>
* <span data-ttu-id="87440-147">Görüntüle</span><span class="sxs-lookup"><span data-stu-id="87440-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="87440-148">Alan seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87440-148">Area options</span></span>

<span data-ttu-id="87440-149">Bu araç, denetleyiciler ve görünümler içeren ASP.NET Core Web projelerine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="87440-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="87440-150">Bu, Razor sayfa uygulamalarına yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="87440-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="87440-151">Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="87440-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="87440-152">Yukarıdaki komut aşağıdaki klasörleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="87440-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="87440-153">*Alanlar*</span><span class="sxs-lookup"><span data-stu-id="87440-153">*Areas*</span></span>
  * <span data-ttu-id="87440-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="87440-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="87440-155">*Denetleyiciler*</span><span class="sxs-lookup"><span data-stu-id="87440-155">*Controllers*</span></span>
    * <span data-ttu-id="87440-156">*Veriler*</span><span class="sxs-lookup"><span data-stu-id="87440-156">*Data*</span></span>
    * <span data-ttu-id="87440-157">*Modeller*</span><span class="sxs-lookup"><span data-stu-id="87440-157">*Models*</span></span>
    * <span data-ttu-id="87440-158">*Görünümler*</span><span class="sxs-lookup"><span data-stu-id="87440-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="87440-159">Denetleyici Seçenekleri</span><span class="sxs-lookup"><span data-stu-id="87440-159">Controller options</span></span>

<span data-ttu-id="87440-160">Aşağıdaki tabloda ve seçenekleri listelenmiştir  `aspnet-codegenerator` `controller` `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="87440-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="87440-161">Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir  `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="87440-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="87440-162">Seçenek</span><span class="sxs-lookup"><span data-stu-id="87440-162">Option</span></span>                         | <span data-ttu-id="87440-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87440-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="87440-164">--controllerName veya-Name</span><span class="sxs-lookup"><span data-stu-id="87440-164">--controllerName or -name</span></span>      | <span data-ttu-id="87440-165">Denetleyicinin adı.</span><span class="sxs-lookup"><span data-stu-id="87440-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="87440-166">--Kullanılan Asyncactions veya-async</span><span class="sxs-lookup"><span data-stu-id="87440-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="87440-167">Zaman uyumsuz denetleyici eylemleri oluştur.</span><span class="sxs-lookup"><span data-stu-id="87440-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="87440-168">--noViews veya-NV</span><span class="sxs-lookup"><span data-stu-id="87440-168">--noViews or -nv</span></span>               | <span data-ttu-id="87440-169">**Hiçbir** görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87440-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="87440-170">--restWithNoViews veya-API</span><span class="sxs-lookup"><span data-stu-id="87440-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="87440-171">REST stili API ile bir denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87440-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="87440-172">`noViews` varsayılır ve tüm görünümle ilgili seçenekler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="87440-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="87440-173">--readWriteActions veya-Actions</span><span class="sxs-lookup"><span data-stu-id="87440-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="87440-174">Model olmadan okuma/yazma eylemleri ile denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87440-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="87440-175">`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="87440-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="87440-176">Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/first-mvc-app/adding-model) `dotnet aspnet-codegenerator controller` .</span><span class="sxs-lookup"><span data-stu-id="87440-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="87440-177">Razorsayfasında</span><span class="sxs-lookup"><span data-stu-id="87440-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="87440-178">Razor Sayfalar, yeni sayfanın adı ve kullanılacak şablon belirtilerek tek tek iskele alınabilir.</span><span class="sxs-lookup"><span data-stu-id="87440-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="87440-179">Desteklenen şablonlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="87440-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="87440-180">Örneğin, aşağıdaki komut *myedit. cshtml* ve *MyEdit.cshtml.cs* oluşturmak için düzenleme şablonunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="87440-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs* :</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="87440-181">Genellikle, şablon ve oluşturulan dosya adı belirtilmez ve aşağıdaki şablonlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="87440-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="87440-182">Aşağıdaki tabloda ve seçenekleri listelenmiştir  `aspnet-codegenerator` `razorpage` `controller` :</span><span class="sxs-lookup"><span data-stu-id="87440-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="87440-183">Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir  `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="87440-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="87440-184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="87440-184">Option</span></span>                        | <span data-ttu-id="87440-185">Açıklama</span><span class="sxs-lookup"><span data-stu-id="87440-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="87440-186">--namespaceName veya-Namespace</span><span class="sxs-lookup"><span data-stu-id="87440-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="87440-187">Oluşturulan PageModel için kullanılacak ad alanının adı</span><span class="sxs-lookup"><span data-stu-id="87440-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="87440-188">--partialView veya-Partial</span><span class="sxs-lookup"><span data-stu-id="87440-188">--partialView or -partial</span></span>     | <span data-ttu-id="87440-189">Kısmi bir görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87440-189">Generate a partial view.</span></span> <span data-ttu-id="87440-190">Bu belirtilirse, düzen seçenekleri-l ve-UDL yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="87440-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="87440-191">--noPageModel veya-NPM</span><span class="sxs-lookup"><span data-stu-id="87440-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="87440-192">Boş şablon için bir PageModel sınıfı oluşturmamı geç</span><span class="sxs-lookup"><span data-stu-id="87440-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="87440-193">`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="87440-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="87440-194">Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .</span><span class="sxs-lookup"><span data-stu-id="87440-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="87440-195">Bkz. [Yapı Iskelesi Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="87440-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
