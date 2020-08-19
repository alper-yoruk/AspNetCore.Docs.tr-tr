---
title: Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin
author: rick-anderson
description: Sayfalardaki eğitim serisinin 2. bölümü Razor .
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4099873142b99afb7f0659dfd9a4fde8bec3081d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633779"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="87ea5-103">Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="87ea5-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="87ea5-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="87ea5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="87ea5-105">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="87ea5-106">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="87ea5-107">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="87ea5-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="87ea5-108">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="87ea5-109">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="87ea5-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="87ea5-110">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="87ea5-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-112">Yeni klasör **eklemek**> \*\* Razor pagesmovie\*\* projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="87ea5-113">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-113">Name the folder *Models*.</span></span>

<span data-ttu-id="87ea5-114">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-114">Right click the *Models* folder.</span></span> <span data-ttu-id="87ea5-115">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="87ea5-116">Sınıf **filmi**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="87ea5-118">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="87ea5-119">*Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="87ea5-121">Çözüm bölmesi, \*\* Razor pagesmovie\*\* projesine sağ tıklayın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="87ea5-122">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="87ea5-123">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="87ea5-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="87ea5-124">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="87ea5-125">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="87ea5-126">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="87ea5-127">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="87ea5-128">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-128">Scaffold the movie model</span></span>

<span data-ttu-id="87ea5-129">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="87ea5-130">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-132">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="87ea5-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="87ea5-133">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="87ea5-134">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-134">Name the folder *Movies*</span></span>

<span data-ttu-id="87ea5-135">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="87ea5-137">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="87ea5-139">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="87ea5-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="87ea5-140">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="87ea5-141">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı Razor pagesmovie 'den değiştirin.\*\* Modeller**. Razor Pagesmovie 'e PagesMovieContext Razor .** Veri\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="87ea5-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="87ea5-142">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="87ea5-143">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="87ea5-144">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-144">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="87ea5-146">Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="87ea5-148">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="87ea5-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="87ea5-149">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="87ea5-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="87ea5-150">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87ea5-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="87ea5-151">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87ea5-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-152">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ea5-153">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="87ea5-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="87ea5-154">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="87ea5-155">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-155">Name the folder *Movies*</span></span>

<span data-ttu-id="87ea5-156">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="87ea5-158">**Yeni yapı iskelesi** iletişim kutusunda, İleri \*\* Razor Entity Framework (CRUD) öğesini kullanarak sayfalar\*\* ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="87ea5-160">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="87ea5-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="87ea5-161">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( Razor pagesmovie. modeller)**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="87ea5-162">**Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, Razor pagesfilmi.\*\* Veri\*\*. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="87ea5-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="87ea5-163">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="87ea5-164">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="87ea5-165">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-165">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="87ea5-167">Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="87ea5-168">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="87ea5-168">Add EF tools</span></span>

<span data-ttu-id="87ea5-169">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87ea5-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="87ea5-170">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="87ea5-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="87ea5-171">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="87ea5-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-173">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="87ea5-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="87ea5-174">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="87ea5-175">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="87ea5-176">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="87ea5-176">Updated</span></span>

* <span data-ttu-id="87ea5-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-177">*Startup.cs*</span></span>

<span data-ttu-id="87ea5-178">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-179">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ea5-180">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="87ea5-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="87ea5-181">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="87ea5-182">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="87ea5-183">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="87ea5-183">Updated</span></span>

* <span data-ttu-id="87ea5-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-184">*Startup.cs*</span></span>

<span data-ttu-id="87ea5-185">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="87ea5-187">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="87ea5-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="87ea5-188">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="87ea5-189">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="87ea5-190">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="87ea5-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-192">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="87ea5-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="87ea5-193">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-193">Add an initial migration.</span></span>
* <span data-ttu-id="87ea5-194">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="87ea5-195">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="87ea5-197">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="87ea5-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="87ea5-200">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="87ea5-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="87ea5-201">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="87ea5-202">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="87ea5-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="87ea5-203">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="87ea5-204">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="87ea5-205">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="87ea5-206">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="87ea5-207">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="87ea5-208">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="87ea5-209">Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="87ea5-211">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="87ea5-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="87ea5-212">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="87ea5-213">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="87ea5-214">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="87ea5-215">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="87ea5-216">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="87ea5-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="87ea5-217">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="87ea5-218">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="87ea5-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="87ea5-219">`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="87ea5-220">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="87ea5-221">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="87ea5-222">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="87ea5-223">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="87ea5-224">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="87ea5-225">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="87ea5-226">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="87ea5-228">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ea5-230">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="87ea5-231">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="87ea5-231">Test the app</span></span>

* <span data-ttu-id="87ea5-232">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="87ea5-233">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="87ea5-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="87ea5-234">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="87ea5-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="87ea5-235">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-235">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="87ea5-237">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="87ea5-238">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="87ea5-239">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="87ea5-240">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="87ea5-241">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87ea5-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="87ea5-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="87ea5-243">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="87ea5-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="87ea5-244">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="87ea5-245">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="87ea5-246">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="87ea5-247">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="87ea5-248">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="87ea5-249">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="87ea5-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="87ea5-250">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="87ea5-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-252">Yeni klasör **eklemek**> \*\* Razor pagesmovie\*\* projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="87ea5-253">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-253">Name the folder *Models*.</span></span>

<span data-ttu-id="87ea5-254">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-254">Right click the *Models* folder.</span></span> <span data-ttu-id="87ea5-255">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="87ea5-256">Sınıf **filmi**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="87ea5-258">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="87ea5-259">*Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="87ea5-261">Çözüm Gezgini, \*\* Razor pagesmovie\*\* projesine sağ tıklayın ve ardından **Add**  >  **Yeni klasör**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="87ea5-262">Klasör *modellerini*adlandırın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="87ea5-263">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya**Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="87ea5-264">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="87ea5-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="87ea5-265">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="87ea5-266">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="87ea5-267">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="87ea5-268">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="87ea5-269">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-269">Scaffold the movie model</span></span>

<span data-ttu-id="87ea5-270">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="87ea5-271">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-273">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="87ea5-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="87ea5-274">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="87ea5-275">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-275">Name the folder *Movies*</span></span>

<span data-ttu-id="87ea5-276">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="87ea5-278">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="87ea5-280">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="87ea5-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="87ea5-281">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="87ea5-282">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan \*\* Razor pagesmovie. modeller adlı adı kabul edin. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="87ea5-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="87ea5-283">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-283">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="87ea5-285">Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="87ea5-287">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="87ea5-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="87ea5-288">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87ea5-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="87ea5-289">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="87ea5-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-290">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ea5-291">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="87ea5-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="87ea5-292">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="87ea5-293">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="87ea5-293">Name the folder *Movies*</span></span>

<span data-ttu-id="87ea5-294">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="87ea5-296">**Yeni yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="87ea5-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="87ea5-298">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="87ea5-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="87ea5-299">**Model sınıfı** açılan kutusunda **film**' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="87ea5-300">**Veri bağlamı sınıfı** satırında, \*\* Razor pagesmoviecontext\*\* ' i seçin. Bu, doğru ad alanı ile yeni bir DB bağlam sınıfı oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="87ea5-301">Bu durumda, \*\* Razor pagesmovie. modeller olacaktır. Razor PagesMovieContext\*\*.</span><span class="sxs-lookup"><span data-stu-id="87ea5-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="87ea5-302">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-302">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="87ea5-304">Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="87ea5-305">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="87ea5-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="87ea5-306">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="87ea5-306">Files created</span></span>

* <span data-ttu-id="87ea5-307">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="87ea5-308">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="87ea5-309">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="87ea5-309">File updated</span></span>

* <span data-ttu-id="87ea5-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="87ea5-310">*Startup.cs*</span></span>

<span data-ttu-id="87ea5-311">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="87ea5-312">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="87ea5-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ea5-314">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="87ea5-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="87ea5-315">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-315">Add an initial migration.</span></span>
* <span data-ttu-id="87ea5-316">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="87ea5-317">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="87ea5-319">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="87ea5-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="87ea5-320">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="87ea5-321">Şema, öğesinde belirtilen modeli temel alır `DbContext` ( \* Razor PagesMovieContext.cs\* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="87ea5-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="87ea5-322">`InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="87ea5-323">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="87ea5-324">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="87ea5-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="87ea5-325">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="87ea5-326">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-328">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="87ea5-329">Yukarıdaki komutlar şu uyarıyı oluşturur: "*' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin.*" Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ea5-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="87ea5-331">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="87ea5-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="87ea5-332">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="87ea5-333">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="87ea5-334">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="87ea5-335">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="87ea5-336">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="87ea5-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="87ea5-337">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="87ea5-338">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="87ea5-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="87ea5-339">`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="87ea5-340">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="87ea5-341">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="87ea5-342">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="87ea5-343">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="87ea5-344">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="87ea5-345">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="87ea5-346">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="87ea5-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ea5-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ea5-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="87ea5-348">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ea5-349">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ea5-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ea5-350">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="87ea5-351">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="87ea5-351">Test the app</span></span>

* <span data-ttu-id="87ea5-352">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="87ea5-353">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="87ea5-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="87ea5-354">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="87ea5-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="87ea5-355">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-355">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="87ea5-357">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="87ea5-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="87ea5-358">Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="87ea5-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="87ea5-359">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="87ea5-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="87ea5-360">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="87ea5-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="87ea5-361">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87ea5-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87ea5-362">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="87ea5-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="87ea5-363">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="87ea5-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
