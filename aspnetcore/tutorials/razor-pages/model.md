---
title: Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin
author: rick-anderson
description: Sayfalardaki eğitim serisinin 2. bölümü Razor .
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: d3f7d355625ce20a0cb45c785fbab259a5655c63
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652924"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="eb764-103">Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="eb764-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="eb764-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb764-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="eb764-105">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="eb764-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="eb764-106">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb764-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="eb764-107">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="eb764-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="eb764-108">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="eb764-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="eb764-109">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="eb764-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="eb764-110">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="eb764-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-112">Yeni klasör **eklemek**> **RazorPagesMovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="eb764-113">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-113">Name the folder *Models*.</span></span>

<span data-ttu-id="eb764-114">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="eb764-114">Right click the *Models* folder.</span></span> <span data-ttu-id="eb764-115">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="eb764-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="eb764-116">Sınıf **filmi**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eb764-118">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="eb764-119">*Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eb764-121">Çözüm bölmesi, **RazorPagesMovie** projesine sağ tıklayın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="eb764-122">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="eb764-123">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="eb764-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="eb764-124">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="eb764-125">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="eb764-126">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="eb764-127">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="eb764-128">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-128">Scaffold the movie model</span></span>

<span data-ttu-id="eb764-129">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="eb764-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="eb764-130">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="eb764-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-132">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="eb764-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eb764-133">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eb764-134">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-134">Name the folder *Movies*</span></span>

<span data-ttu-id="eb764-135">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="eb764-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="eb764-137">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="eb764-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="eb764-139">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="eb764-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eb764-140">**Model sınıfı** açılan kutusunda **Film (RazorPagesMovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eb764-141">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı RazorPagesMovie ' dan değiştirin.\*\* Modeller\*\*. RazorPagesMovieContext to RazorPagesMovie. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="eb764-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="eb764-142">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="eb764-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="eb764-143">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eb764-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="eb764-144">**Ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-144">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="eb764-146">*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="eb764-148">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="eb764-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="eb764-149">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="eb764-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="eb764-150">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eb764-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="eb764-151">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eb764-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-152">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb764-153">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="eb764-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eb764-154">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eb764-155">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-155">Name the folder *Movies*</span></span>

<span data-ttu-id="eb764-156">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.</span><span class="sxs-lookup"><span data-stu-id="eb764-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="eb764-158">**Yeni yapı iskelesi** iletişim kutusunda, İleri \*\* Razor Entity Framework (CRUD) öğesini kullanarak sayfalar\*\* ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="eb764-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="eb764-160">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="eb764-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eb764-161">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film (RazorPagesMovie. modeller)**.</span><span class="sxs-lookup"><span data-stu-id="eb764-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eb764-162">**Veri bağlamı sınıfı** satırına, RazorPagesMovie adlı yeni sınıfın adını yazın. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="eb764-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="eb764-163">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="eb764-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="eb764-164">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eb764-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="eb764-165">**Ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-165">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="eb764-167">*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="eb764-168">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="eb764-168">Add EF tools</span></span>

<span data-ttu-id="eb764-169">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eb764-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="eb764-170">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="eb764-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="eb764-171">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="eb764-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-173">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="eb764-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="eb764-174">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="eb764-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eb764-175">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="eb764-176">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="eb764-176">Updated</span></span>

* <span data-ttu-id="eb764-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-177">*Startup.cs*</span></span>

<span data-ttu-id="eb764-178">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-179">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb764-180">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="eb764-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="eb764-181">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="eb764-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eb764-182">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="eb764-183">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="eb764-183">Updated</span></span>

* <span data-ttu-id="eb764-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-184">*Startup.cs*</span></span>

<span data-ttu-id="eb764-185">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb764-187">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="eb764-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="eb764-188">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="eb764-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="eb764-189">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="eb764-190">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="eb764-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-192">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="eb764-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="eb764-193">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-193">Add an initial migration.</span></span>
* <span data-ttu-id="eb764-194">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="eb764-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="eb764-195">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="eb764-197">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="eb764-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="eb764-200">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="eb764-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="eb764-201">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="eb764-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="eb764-202">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="eb764-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="eb764-203">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="eb764-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="eb764-204">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="eb764-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="eb764-205">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="eb764-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="eb764-206">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eb764-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="eb764-207">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="eb764-208">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="eb764-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="eb764-209">Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="eb764-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="eb764-211">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="eb764-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="eb764-212">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="eb764-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb764-213">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="eb764-214">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="eb764-215">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="eb764-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="eb764-216">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="eb764-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="eb764-217">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb764-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eb764-218">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="eb764-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="eb764-219">`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="eb764-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="eb764-220">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="eb764-221">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="eb764-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="eb764-222">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eb764-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="eb764-223">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="eb764-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="eb764-224">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="eb764-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="eb764-225">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="eb764-226">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="eb764-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb764-228">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="eb764-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb764-230">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="eb764-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="eb764-231">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="eb764-231">Test the app</span></span>

* <span data-ttu-id="eb764-232">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="eb764-233">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="eb764-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="eb764-234">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="eb764-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="eb764-235">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="eb764-235">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="eb764-237">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="eb764-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="eb764-238">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb764-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="eb764-239">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb764-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="eb764-240">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="eb764-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="eb764-241">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb764-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb764-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eb764-243">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="eb764-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb764-244">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="eb764-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="eb764-245">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="eb764-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="eb764-246">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eb764-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="eb764-247">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="eb764-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="eb764-248">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="eb764-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="eb764-249">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="eb764-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="eb764-250">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="eb764-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-252">Yeni klasör **eklemek**> **RazorPagesMovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="eb764-253">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-253">Name the folder *Models*.</span></span>

<span data-ttu-id="eb764-254">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="eb764-254">Right click the *Models* folder.</span></span> <span data-ttu-id="eb764-255">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="eb764-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="eb764-256">Sınıf **filmi**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="eb764-258">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="eb764-259">*Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="eb764-261">Çözüm Gezgini, **RazorPagesMovie** projesine sağ tıklayın ve ardından **Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="eb764-262">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="eb764-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="eb764-263">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="eb764-264">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="eb764-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="eb764-265">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="eb764-266">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="eb764-267">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="eb764-268">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="eb764-269">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-269">Scaffold the movie model</span></span>

<span data-ttu-id="eb764-270">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="eb764-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="eb764-271">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="eb764-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-273">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="eb764-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eb764-274">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eb764-275">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-275">Name the folder *Movies*</span></span>

<span data-ttu-id="eb764-276">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="eb764-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="eb764-278">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="eb764-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="eb764-280">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="eb764-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="eb764-281">**Model sınıfı** açılan kutusunda **Film (RazorPagesMovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="eb764-282">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **RazorPagesMovie. modeller. RazorPagesMovieContext**adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="eb764-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="eb764-283">**Ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-283">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="eb764-285">*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="eb764-287">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="eb764-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="eb764-288">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eb764-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="eb764-289">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="eb764-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-290">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb764-291">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="eb764-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="eb764-292">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="eb764-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="eb764-293">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="eb764-293">Name the folder *Movies*</span></span>

<span data-ttu-id="eb764-294">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="eb764-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="eb764-296">**Yeni yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="eb764-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="eb764-298">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="eb764-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="eb764-299">**Model sınıfı** açılan kutusunda **film**' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="eb764-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="eb764-300">**Veri bağlamı sınıfı** satırına **RazorPagesMovieContext** öğesini seçin. Bu, doğru ad alanı ile yeni bir DB bağlam sınıfı oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="eb764-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="eb764-301">Bu durumda, **RazorPagesMovie. modeller. RazorPagesMovieContext**olacaktır.</span><span class="sxs-lookup"><span data-stu-id="eb764-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="eb764-302">**Ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-302">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="eb764-304">*AppSettings. JSON* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="eb764-305">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="eb764-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="eb764-306">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="eb764-306">Files created</span></span>

* <span data-ttu-id="eb764-307">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="eb764-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="eb764-308">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="eb764-309">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="eb764-309">File updated</span></span>

* <span data-ttu-id="eb764-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="eb764-310">*Startup.cs*</span></span>

<span data-ttu-id="eb764-311">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="eb764-312">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="eb764-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eb764-314">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="eb764-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="eb764-315">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-315">Add an initial migration.</span></span>
* <span data-ttu-id="eb764-316">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="eb764-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="eb764-317">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="eb764-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="eb764-319">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="eb764-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="eb764-320">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="eb764-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="eb764-321">Şema, öğesinde belirtilen modeli temel alır `DbContext` ( *RazorPagesMovieContext.cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="eb764-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="eb764-322">`InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eb764-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="eb764-323">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="eb764-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="eb764-324">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="eb764-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="eb764-325">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="eb764-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="eb764-326">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eb764-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-328">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="eb764-329">Yukarıdaki komutlar şu uyarıyı oluşturur: "*' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin.*" Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="eb764-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eb764-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="eb764-331">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="eb764-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="eb764-332">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="eb764-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="eb764-333">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="eb764-334">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="eb764-335">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="eb764-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="eb764-336">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="eb764-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="eb764-337">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="eb764-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eb764-338">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="eb764-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="eb764-339">`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="eb764-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="eb764-340">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="eb764-341">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="eb764-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="eb764-342">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="eb764-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="eb764-343">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="eb764-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="eb764-344">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="eb764-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="eb764-345">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="eb764-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="eb764-346">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="eb764-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eb764-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eb764-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eb764-348">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="eb764-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eb764-349">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eb764-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eb764-350">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="eb764-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="eb764-351">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="eb764-351">Test the app</span></span>

* <span data-ttu-id="eb764-352">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="eb764-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="eb764-353">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="eb764-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="eb764-354">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="eb764-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="eb764-355">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="eb764-355">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="eb764-357">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="eb764-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="eb764-358">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="eb764-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="eb764-359">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="eb764-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="eb764-360">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="eb764-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="eb764-361">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="eb764-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb764-362">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="eb764-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="eb764-363">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="eb764-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
