---
title: ASP.NET Core'daki Razor Pages uygulamasına model ekleme
author: rick-anderson
description: Entity Framework Core (EF Core) kullanarak bir veritabanında film yönetimi için sınıfları nasıl ekleyeceğinizi keşfedin.
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658937"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="2c441-103">ASP.NET Core'daki Razor Pages uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="2c441-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2c441-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c441-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2c441-105">Bu bölümde, bir çapraz platform [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetmek için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="2c441-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2c441-106">ASP.NET Core şablonundan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2c441-107">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2c441-108">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisieşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="2c441-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2c441-109">MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur.</span><span class="sxs-lookup"><span data-stu-id="2c441-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2c441-110">Veritabanında depolanan verilerin özelliklerini tanımlarlar.</span><span class="sxs-lookup"><span data-stu-id="2c441-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2c441-111">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="2c441-111">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-113">**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** > </span><span class="sxs-lookup"><span data-stu-id="2c441-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2c441-114">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2c441-114">Name the folder *Models*.</span></span>

<span data-ttu-id="2c441-115">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-115">Right click the *Models* folder.</span></span> <span data-ttu-id="2c441-116">**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="2c441-117">Sınıf **Film**adı .</span><span class="sxs-lookup"><span data-stu-id="2c441-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c441-119">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2c441-120">*Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-121">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c441-122">Solution Pad'de **RazorPagesMovie** projesine sağ tıklayın ve **ardından Yeni Klasör Ekle'yi** **Add** > seçin... seçeneğini belirleyin. *Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2c441-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2c441-123">*Modeller* klasörüne sağ tıklayın ve **ardından Yeni Dosya Ekle'yi** **Add** > seçin... seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2c441-124">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2c441-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2c441-125">Sol bölmede **Genel'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2c441-126">Orta bölmede **Boş Sınıf'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2c441-127">Sınıf **Film** adını ve **Yeni**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2c441-128">Derleme hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2c441-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2c441-129">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="2c441-129">Scaffold the movie model</span></span>

<span data-ttu-id="2c441-130">Bu bölümde, film modeli iskele.</span><span class="sxs-lookup"><span data-stu-id="2c441-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2c441-131">Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-133">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2c441-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2c441-134">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2c441-135">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="2c441-135">Name the folder *Movies*</span></span>

<span data-ttu-id="2c441-136">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="2c441-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/sca.png)

<span data-ttu-id="2c441-138">İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**</span><span class="sxs-lookup"><span data-stu-id="2c441-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="2c441-140">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="2c441-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2c441-141">Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="2c441-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2c441-142">Veri **bağlamı sınıf** satırında(artı) işaretini **+** seçin ve RazorPagesMovie'den oluşturulan adı değiştirin. **Modeller**. RazorPagesMovieContext için RazorPagesMovieMovie. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2c441-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2c441-143">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2c441-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2c441-144">Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2c441-145">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-145">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/3/arp.png)

<span data-ttu-id="2c441-147">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2c441-149">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="2c441-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2c441-150">İskele aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c441-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="2c441-151">**Windows için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2c441-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2c441-152">**macOS ve Linux için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2c441-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-153">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c441-154">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2c441-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2c441-155">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2c441-156">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="2c441-156">Name the folder *Movies*</span></span>

<span data-ttu-id="2c441-157">*Sayfalar/Filmler* klasörüne sağ tıklayın > Yeni İskele **Ekle...** > **New Scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="2c441-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

<span data-ttu-id="2c441-159">Yeni **İskele** iletişim kutusunda, Entity Framework > **(CRUD) Next'i kullanarak Razor Pages'ı** seçin. **Next**</span><span class="sxs-lookup"><span data-stu-id="2c441-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2c441-161">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="2c441-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2c441-162">Model **sınıfında** film **(RazorPagesMovie.Models)** aşağı, seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="2c441-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2c441-163">Veri **bağlamında sınıf** satırında, yeni sınıfın adını yazın, RazorPagesMovie. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="2c441-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2c441-164">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="2c441-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2c441-165">Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2c441-166">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-166">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

<span data-ttu-id="2c441-168">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2c441-169">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="2c441-169">Add EF tools</span></span>

<span data-ttu-id="2c441-170">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2c441-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2c441-171">Önceki komut .NET Core CLI için Entity Framework Core Tools ekler.</span><span class="sxs-lookup"><span data-stu-id="2c441-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="2c441-172">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="2c441-172">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-174">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="2c441-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2c441-175">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="2c441-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2c441-176">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2c441-177">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="2c441-177">Updated</span></span>

* <span data-ttu-id="2c441-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-178">*Startup.cs*</span></span>

<span data-ttu-id="2c441-179">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-180">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c441-181">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="2c441-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2c441-182">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="2c441-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2c441-183">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2c441-184">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="2c441-184">Updated</span></span>

* <span data-ttu-id="2c441-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-185">*Startup.cs*</span></span>

<span data-ttu-id="2c441-186">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c441-188">İskele işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2c441-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2c441-189">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="2c441-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2c441-190">Oluşturulan dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2c441-191">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="2c441-191">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-193">Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2c441-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2c441-194">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-194">Add an initial migration.</span></span>
* <span data-ttu-id="2c441-195">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2c441-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="2c441-196">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2c441-198">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="2c441-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-200">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="2c441-201">Önceki komutlar şu uyarıyı oluşturur: "Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="2c441-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2c441-202">Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2c441-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2c441-203">'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin."</span><span class="sxs-lookup"><span data-stu-id="2c441-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2c441-204">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="2c441-205">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2c441-206">Şema, 'de `DbContext`belirtilen modele dayanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2c441-207">Bağımsız `InitialCreate` değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2c441-208">Herhangi bir ad kullanılabilir, ancak kural olarak geçişi açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2c441-209">Komut, `update` uygulanmamış geçişlerde `Up` yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="2c441-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2c441-210">Bu durumda, `update` veritabanını `Up` oluşturan *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntem çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c441-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2c441-212">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="2c441-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2c441-213">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2c441-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2c441-214">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2c441-215">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2c441-216">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2c441-217">İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="2c441-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2c441-218">`Startup.ConfigureServices` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2c441-219">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="2c441-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2c441-220">Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie`</span><span class="sxs-lookup"><span data-stu-id="2c441-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2c441-221">Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2c441-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2c441-222">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c441-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2c441-223">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2c441-224">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2c441-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2c441-225">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2c441-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2c441-226">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2c441-227">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="2c441-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c441-229">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-230">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c441-231">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2c441-232">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="2c441-232">Test the app</span></span>

* <span data-ttu-id="2c441-233">Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).</span><span class="sxs-lookup"><span data-stu-id="2c441-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2c441-234">Hata alırsanız:</span><span class="sxs-lookup"><span data-stu-id="2c441-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2c441-235">[Göçler adımını](#pmc)kaçırdın.</span><span class="sxs-lookup"><span data-stu-id="2c441-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2c441-236">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="2c441-236">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2c441-238">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2c441-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2c441-239">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c441-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2c441-240">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="2c441-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2c441-241">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="2c441-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2c441-242">Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.</span><span class="sxs-lookup"><span data-stu-id="2c441-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c441-243">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c441-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2c441-244">[Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2c441-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c441-245">Bu bölümde, bir çapraz platform [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetmek için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="2c441-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2c441-246">ASP.NET Core şablonundan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2c441-247">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2c441-248">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisieşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="2c441-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2c441-249">MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur.</span><span class="sxs-lookup"><span data-stu-id="2c441-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2c441-250">Veritabanında depolanan verilerin özelliklerini tanımlarlar.</span><span class="sxs-lookup"><span data-stu-id="2c441-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2c441-251">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="2c441-251">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-253">**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** > </span><span class="sxs-lookup"><span data-stu-id="2c441-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2c441-254">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2c441-254">Name the folder *Models*.</span></span>

<span data-ttu-id="2c441-255">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-255">Right click the *Models* folder.</span></span> <span data-ttu-id="2c441-256">**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="2c441-257">Sınıf **Film**adı .</span><span class="sxs-lookup"><span data-stu-id="2c441-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c441-259">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2c441-260">*Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-261">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2c441-262">Solution Explorer'da **RazorPagesMovie** projesini sağ tıklatın ve ardından**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2c441-263">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="2c441-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="2c441-264">*Modeller* klasörüne sağ tıklayın ve ardından **Yeni Dosya** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2c441-265">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="2c441-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2c441-266">Sol bölmede **Genel'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2c441-267">Orta bölmede **Boş Sınıf'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2c441-268">Sınıf **Film** adını ve **Yeni**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="2c441-269">Derleme hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2c441-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2c441-270">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="2c441-270">Scaffold the movie model</span></span>

<span data-ttu-id="2c441-271">Bu bölümde, film modeli iskele.</span><span class="sxs-lookup"><span data-stu-id="2c441-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2c441-272">Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-274">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2c441-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2c441-275">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2c441-276">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="2c441-276">Name the folder *Movies*</span></span>

<span data-ttu-id="2c441-277">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="2c441-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/sca.png)

<span data-ttu-id="2c441-279">İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**</span><span class="sxs-lookup"><span data-stu-id="2c441-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="2c441-281">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="2c441-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2c441-282">Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="2c441-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2c441-283">Veri **bağlamında sınıf** satırında(artı) işaretini **+** seçin ve oluşturulan **razorPagesMovie.Models.RazorPagesMovieContext**adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="2c441-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2c441-284">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-284">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arp.png)

<span data-ttu-id="2c441-286">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2c441-288">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="2c441-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="2c441-289">**Windows için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2c441-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2c441-290">**macOS ve Linux için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2c441-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-291">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c441-292">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2c441-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2c441-293">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="2c441-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2c441-294">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="2c441-294">Name the folder *Movies*</span></span>

<span data-ttu-id="2c441-295">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="2c441-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

<span data-ttu-id="2c441-297">Yeni **İskele Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **Ekle'yi**kullanarak Jilet Sayfaları'nı seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2c441-299">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="2c441-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2c441-300">Model **sınıfında** aşağı açılan, **Film**seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="2c441-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2c441-301">Veri **bağlamı sınıf** satırında, **RazorPagesMovieContext'ı** yazın, bu da doğru ad alanına sahip yeni bir db bağlam sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="2c441-302">Bu durumda **RazorPagesMovie.Models.RazorPagesMovieContext**olacak .</span><span class="sxs-lookup"><span data-stu-id="2c441-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2c441-303">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-303">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

<span data-ttu-id="2c441-305">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2c441-306">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="2c441-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2c441-307">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="2c441-307">Files created</span></span>

* <span data-ttu-id="2c441-308">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="2c441-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2c441-309">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2c441-310">Dosya güncellendi</span><span class="sxs-lookup"><span data-stu-id="2c441-310">File updated</span></span>

* <span data-ttu-id="2c441-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2c441-311">*Startup.cs*</span></span>

<span data-ttu-id="2c441-312">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2c441-313">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="2c441-313">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c441-315">Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2c441-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2c441-316">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-316">Add an initial migration.</span></span>
* <span data-ttu-id="2c441-317">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2c441-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="2c441-318">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="2c441-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2c441-320">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="2c441-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2c441-321">Komut, `Add-Migration` ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2c441-322">Şema,(RazorPagesMovieContext.cs dosyasında) belirtilen modele dayanır. *RazorPagesMovieContext.cs* `DbContext`</span><span class="sxs-lookup"><span data-stu-id="2c441-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2c441-323">Bağımsız `InitialCreate` değişken, geçişin adını vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2c441-324">Herhangi bir ad kullanılabilir, ancak kuralıile geçiş açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2c441-325">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="2c441-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2c441-326">Komut, `Update-Database` `Up` *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="2c441-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2c441-327">Yöntem `Up` veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-329">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="2c441-330">Önceki komutlar şu uyarıyı oluşturur: "*Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi. Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur. 'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin.* Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c441-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2c441-332">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="2c441-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2c441-333">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2c441-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2c441-334">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2c441-335">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c441-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2c441-336">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="2c441-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2c441-337">İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="2c441-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2c441-338">`Startup.ConfigureServices` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2c441-339">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="2c441-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2c441-340">Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie`</span><span class="sxs-lookup"><span data-stu-id="2c441-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2c441-341">Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="2c441-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2c441-342">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c441-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2c441-343">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c441-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2c441-344">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2c441-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2c441-345">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2c441-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2c441-346">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="2c441-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2c441-347">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="2c441-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c441-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c441-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c441-349">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c441-350">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c441-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c441-351">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="2c441-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2c441-352">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="2c441-352">Test the app</span></span>

* <span data-ttu-id="2c441-353">Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).</span><span class="sxs-lookup"><span data-stu-id="2c441-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2c441-354">Hata alırsanız:</span><span class="sxs-lookup"><span data-stu-id="2c441-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2c441-355">[Göçler adımını](#pmc)kaçırdın.</span><span class="sxs-lookup"><span data-stu-id="2c441-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2c441-356">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="2c441-356">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2c441-358">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2c441-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2c441-359">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c441-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2c441-360">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="2c441-360">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2c441-361">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="2c441-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2c441-362">Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.</span><span class="sxs-lookup"><span data-stu-id="2c441-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c441-363">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c441-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2c441-364">[Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2c441-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
