---
title: DotNet ASPNET-CodeGenerator komutu
author: rick-anderson
description: DotNet ASPNET-CodeGenerator komutu yapı ASP.NET Core projeler.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: af151b65d3fefb5863529de1cd97c6315f11ca82
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444120"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="86301-103">DotNet ASPNET-CodeGenerator</span><span class="sxs-lookup"><span data-stu-id="86301-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="86301-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="86301-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="86301-105">`dotnet aspnet-codegenerator`-ASP.NET Core scafkatlama altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="86301-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="86301-106">`dotnet aspnet-codegenerator`yalnızca komut satırından yapı iskelesi sağlamak için gereklidir, Visual Studio ile scafkatlamayı kullanmak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="86301-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="86301-107">Bu makale [.NET Core 2,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) ve üzeri için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="86301-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="86301-108">ASPNET-CodeGenerator yükleniyor</span><span class="sxs-lookup"><span data-stu-id="86301-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="86301-109">`dotnet-aspnet-codegenerator`yüklenmesi gereken [küresel bir araçtır](/dotnet/core/tools/global-tools) .</span><span class="sxs-lookup"><span data-stu-id="86301-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="86301-110">Aşağıdaki komut aracın en son kararlı sürümünü yüklüyor `dotnet-aspnet-codegenerator` :</span><span class="sxs-lookup"><span data-stu-id="86301-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="86301-111">Aşağıdaki komut, `dotnet-aspnet-codegenerator` yüklü .NET Core SDK 'larında kullanılabilen en son kararlı sürümü güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="86301-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="86301-112">Özeti</span><span class="sxs-lookup"><span data-stu-id="86301-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="86301-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="86301-113">Description</span></span>

<span data-ttu-id="86301-114">`dotnet aspnet-codegenerator`Genel komut ASP.NET Core kod Oluşturucu ve yapı iskelesi altyapısını çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="86301-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="86301-115">Arguments</span><span class="sxs-lookup"><span data-stu-id="86301-115">Arguments</span></span>

`generator`

<span data-ttu-id="86301-116">Çalıştırılacak kod Oluşturucu.</span><span class="sxs-lookup"><span data-stu-id="86301-116">The code generator to run.</span></span> <span data-ttu-id="86301-117">Aşağıdaki oluşturucular kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="86301-117">The following generators are available:</span></span>

| <span data-ttu-id="86301-118">Oluşturucu</span><span class="sxs-lookup"><span data-stu-id="86301-118">Generator</span></span>  | <span data-ttu-id="86301-119">İşlem</span><span class="sxs-lookup"><span data-stu-id="86301-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="86301-120">alan</span><span class="sxs-lookup"><span data-stu-id="86301-120">area</span></span>       | [<span data-ttu-id="86301-121">Bir alanı dolandırın</span><span class="sxs-lookup"><span data-stu-id="86301-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="86301-122">denetleyici</span><span class="sxs-lookup"><span data-stu-id="86301-122">controller</span></span> | [<span data-ttu-id="86301-123">Bir denetleyiciyi yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="86301-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="86301-124">identity</span><span class="sxs-lookup"><span data-stu-id="86301-124">identity</span></span>   | [<span data-ttu-id="86301-125">İşleyicilerinIdentity</span><span class="sxs-lookup"><span data-stu-id="86301-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="86301-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="86301-126">razorpage</span></span>  | [<span data-ttu-id="86301-127">Yapı iskelesi Razor sayfaları</span><span class="sxs-lookup"><span data-stu-id="86301-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="86301-128">görüntüle</span><span class="sxs-lookup"><span data-stu-id="86301-128">view</span></span>       | [<span data-ttu-id="86301-129">Bir görünümü dolandırın</span><span class="sxs-lookup"><span data-stu-id="86301-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="86301-130">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="86301-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="86301-131">NuGet paket dizinini belirtir.</span><span class="sxs-lookup"><span data-stu-id="86301-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="86301-132">Yapı yapılandırmasını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="86301-132">Defines the build configuration.</span></span> <span data-ttu-id="86301-133">Varsayılan değer: `Debug`.</span><span class="sxs-lookup"><span data-stu-id="86301-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="86301-134">Kullanılacak hedef [çerçeve](/dotnet/standard/frameworks) .</span><span class="sxs-lookup"><span data-stu-id="86301-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="86301-135">Örneğin, `net46`.</span><span class="sxs-lookup"><span data-stu-id="86301-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="86301-136">Yapı temel yolu.</span><span class="sxs-lookup"><span data-stu-id="86301-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="86301-137">Komut için kısa bir yardım yazdırır.</span><span class="sxs-lookup"><span data-stu-id="86301-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="86301-138">Çalıştırmadan önce projeyi oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="86301-138">Doesn't build the project before running.</span></span> <span data-ttu-id="86301-139">Ayrıca bayrağı örtülü olarak ayarlar `--no-restore` .</span><span class="sxs-lookup"><span data-stu-id="86301-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="86301-140">Çalıştırılacak proje dosyasının yolunu belirtir (klasör adı veya tam yol).</span><span class="sxs-lookup"><span data-stu-id="86301-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="86301-141">Belirtilmezse, varsayılan olarak geçerli dizini alır.</span><span class="sxs-lookup"><span data-stu-id="86301-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="86301-142">Oluşturucu seçenekleri</span><span class="sxs-lookup"><span data-stu-id="86301-142">Generator options</span></span>

<span data-ttu-id="86301-143">Aşağıdaki bölümler, desteklenen oluşturucular için kullanılabilen seçenekleri ayrıntılandırır:</span><span class="sxs-lookup"><span data-stu-id="86301-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="86301-144">Alan</span><span class="sxs-lookup"><span data-stu-id="86301-144">Area</span></span>
* <span data-ttu-id="86301-145">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="86301-145">Controller</span></span>
* Identity  
* <span data-ttu-id="86301-146">Razorsayfasında</span><span class="sxs-lookup"><span data-stu-id="86301-146">Razorpage</span></span>
* <span data-ttu-id="86301-147">Görüntüle</span><span class="sxs-lookup"><span data-stu-id="86301-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="86301-148">Alan seçenekleri</span><span class="sxs-lookup"><span data-stu-id="86301-148">Area options</span></span>

<span data-ttu-id="86301-149">Bu araç, denetleyiciler ve görünümler içeren ASP.NET Core Web projelerine yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="86301-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="86301-150">Bu, Razor sayfa uygulamalarına yönelik değildir.</span><span class="sxs-lookup"><span data-stu-id="86301-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="86301-151">Kullanım: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="86301-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="86301-152">Yukarıdaki komut aşağıdaki klasörleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="86301-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="86301-153">*Alanlar*</span><span class="sxs-lookup"><span data-stu-id="86301-153">*Areas*</span></span>
  * <span data-ttu-id="86301-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="86301-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="86301-155">*Denetleyiciler*</span><span class="sxs-lookup"><span data-stu-id="86301-155">*Controllers*</span></span>
    * <span data-ttu-id="86301-156">*Veriler*</span><span class="sxs-lookup"><span data-stu-id="86301-156">*Data*</span></span>
    * <span data-ttu-id="86301-157">*Modeller*</span><span class="sxs-lookup"><span data-stu-id="86301-157">*Models*</span></span>
    * <span data-ttu-id="86301-158">*Görünümler*</span><span class="sxs-lookup"><span data-stu-id="86301-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="86301-159">Denetleyici Seçenekleri</span><span class="sxs-lookup"><span data-stu-id="86301-159">Controller options</span></span>

<span data-ttu-id="86301-160">Aşağıdaki tabloda ve seçenekleri listelenmiştir `aspnet-codegenerator` `controller` `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="86301-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="86301-161">Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="86301-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="86301-162">Seçenek</span><span class="sxs-lookup"><span data-stu-id="86301-162">Option</span></span>                         | <span data-ttu-id="86301-163">Açıklama</span><span class="sxs-lookup"><span data-stu-id="86301-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="86301-164">--controllerName veya-Name</span><span class="sxs-lookup"><span data-stu-id="86301-164">--controllerName or -name</span></span>      | <span data-ttu-id="86301-165">Denetleyicinin adı.</span><span class="sxs-lookup"><span data-stu-id="86301-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="86301-166">--Kullanılan Asyncactions veya-async</span><span class="sxs-lookup"><span data-stu-id="86301-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="86301-167">Zaman uyumsuz denetleyici eylemleri oluştur.</span><span class="sxs-lookup"><span data-stu-id="86301-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="86301-168">--noViews veya-NV</span><span class="sxs-lookup"><span data-stu-id="86301-168">--noViews or -nv</span></span>               | <span data-ttu-id="86301-169">**Hiçbir** görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="86301-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="86301-170">--restWithNoViews veya-API</span><span class="sxs-lookup"><span data-stu-id="86301-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="86301-171">REST stili API ile bir denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="86301-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="86301-172">`noViews`varsayılır ve tüm görünümle ilgili seçenekler yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="86301-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="86301-173">--readWriteActions veya-Actions</span><span class="sxs-lookup"><span data-stu-id="86301-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="86301-174">Model olmadan okuma/yazma eylemleri ile denetleyici oluşturun.</span><span class="sxs-lookup"><span data-stu-id="86301-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="86301-175">`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="86301-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="86301-176">Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/first-mvc-app/adding-model) `dotnet aspnet-codegenerator controller` .</span><span class="sxs-lookup"><span data-stu-id="86301-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="86301-177">Razorsayfasında</span><span class="sxs-lookup"><span data-stu-id="86301-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="86301-178">RazorSayfalar, yeni sayfanın adı ve kullanılacak şablon belirtilerek tek tek iskele alınabilir.</span><span class="sxs-lookup"><span data-stu-id="86301-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="86301-179">Desteklenen şablonlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="86301-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="86301-180">Örneğin, aşağıdaki komut *myedit. cshtml* ve *MyEdit.cshtml.cs*oluşturmak için düzenleme şablonunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="86301-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="86301-181">Genellikle, şablon ve oluşturulan dosya adı belirtilmez ve aşağıdaki şablonlar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="86301-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="86301-182">Aşağıdaki tabloda ve seçenekleri listelenmiştir `aspnet-codegenerator` `razorpage` `controller` :</span><span class="sxs-lookup"><span data-stu-id="86301-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="86301-183">Aşağıdaki tabloda aşağıdakiler için benzersiz seçenekler listelenmektedir `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="86301-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="86301-184">Seçenek</span><span class="sxs-lookup"><span data-stu-id="86301-184">Option</span></span>                        | <span data-ttu-id="86301-185">Açıklama</span><span class="sxs-lookup"><span data-stu-id="86301-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="86301-186">--namespaceName veya-Namespace</span><span class="sxs-lookup"><span data-stu-id="86301-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="86301-187">Oluşturulan PageModel için kullanılacak ad alanının adı</span><span class="sxs-lookup"><span data-stu-id="86301-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="86301-188">--partialView veya-Partial</span><span class="sxs-lookup"><span data-stu-id="86301-188">--partialView or -partial</span></span>     | <span data-ttu-id="86301-189">Kısmi bir görünüm oluşturun.</span><span class="sxs-lookup"><span data-stu-id="86301-189">Generate a partial view.</span></span> <span data-ttu-id="86301-190">Bu belirtilirse, düzen seçenekleri-l ve-UDL yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="86301-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="86301-191">--noPageModel veya-NPM</span><span class="sxs-lookup"><span data-stu-id="86301-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="86301-192">Boş şablon için bir PageModel sınıfı oluşturmamı geç</span><span class="sxs-lookup"><span data-stu-id="86301-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="86301-193">`-h`Komutuyla ilgili yardım için anahtarı kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="86301-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="86301-194">Bir örneği için bkz. [film modelini yapı iskelesi](xref:tutorials/razor-pages/model) `dotnet aspnet-codegenerator razorpage` .</span><span class="sxs-lookup"><span data-stu-id="86301-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="86301-195">Bkz. [Yapı Iskelesi Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="86301-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
