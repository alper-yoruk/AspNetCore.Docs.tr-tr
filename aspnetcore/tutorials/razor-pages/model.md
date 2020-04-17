---
title: ASP.NET Core'daki Razor Pages uygulamasına model ekleme
author: rick-anderson
description: Entity Framework Core (EF Core) kullanarak bir veritabanında film yönetimi için sınıfları nasıl ekleyeceğinizi keşfedin.
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488877"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="cc315-103">ASP.NET Core'daki Razor Pages uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="cc315-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="cc315-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc315-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="cc315-105">Bu bölümde, filmleri yönetmek için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="cc315-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="cc315-106">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core'u (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="cc315-107">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisisel mapper (O/RM) dir.</span><span class="sxs-lookup"><span data-stu-id="cc315-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="cc315-108">MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur.</span><span class="sxs-lookup"><span data-stu-id="cc315-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cc315-109">Veritabanında depolanan verilerin özelliklerini tanımlarlar.</span><span class="sxs-lookup"><span data-stu-id="cc315-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cc315-110">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="cc315-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-112">**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** > </span><span class="sxs-lookup"><span data-stu-id="cc315-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cc315-113">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cc315-113">Name the folder *Models*.</span></span>

<span data-ttu-id="cc315-114">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-114">Right click the *Models* folder.</span></span> <span data-ttu-id="cc315-115">**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="cc315-116">Sınıf **Film**adı .</span><span class="sxs-lookup"><span data-stu-id="cc315-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cc315-118">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cc315-119">*Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cc315-121">Solution Pad'de **RazorPagesMovie** projesine sağ tıklayın ve **ardından Yeni Klasör Ekle'yi** **Add** > seçin... seçeneğini belirleyin. *Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cc315-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="cc315-122">*Modeller* klasörüne sağ tıklayın ve **ardından Yeni Dosya Ekle'yi** **Add** > seçin... seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="cc315-123">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="cc315-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cc315-124">Sol bölmede **Genel'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cc315-125">Orta bölmede **Boş Sınıf'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cc315-126">Sınıf **Film** adını ve **Yeni**seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cc315-127">Derleme hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cc315-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cc315-128">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="cc315-128">Scaffold the movie model</span></span>

<span data-ttu-id="cc315-129">Bu bölümde, film modeli iskele.</span><span class="sxs-lookup"><span data-stu-id="cc315-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cc315-130">Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-132">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cc315-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc315-133">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc315-134">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="cc315-134">Name the folder *Movies*</span></span>

<span data-ttu-id="cc315-135">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="cc315-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/sca.png)

<span data-ttu-id="cc315-137">İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**</span><span class="sxs-lookup"><span data-stu-id="cc315-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="cc315-139">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="cc315-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc315-140">Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="cc315-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc315-141">Veri **bağlamı sınıf** satırında(artı) işaretini **+** seçin ve RazorPagesMovie'den oluşturulan adı değiştirin. **Modeller**. RazorPagesMovieContext için RazorPagesMovieMovie. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cc315-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cc315-142">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="cc315-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cc315-143">Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cc315-144">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-144">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/3/arp.png)

<span data-ttu-id="cc315-146">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cc315-148">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="cc315-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cc315-149">İskele aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="cc315-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cc315-150">**Windows için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cc315-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cc315-151">**macOS ve Linux için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cc315-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-152">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc315-153">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cc315-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc315-154">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc315-155">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="cc315-155">Name the folder *Movies*</span></span>

<span data-ttu-id="cc315-156">*Sayfalar/Filmler* klasörüne sağ tıklayın > Yeni İskele **Ekle...** > **New Scaffolding...**.</span><span class="sxs-lookup"><span data-stu-id="cc315-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

<span data-ttu-id="cc315-158">Yeni **İskele** iletişim kutusunda, Entity Framework > **(CRUD) Next'i kullanarak Razor Pages'ı** seçin. **Next**</span><span class="sxs-lookup"><span data-stu-id="cc315-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cc315-160">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="cc315-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc315-161">Model **sınıfında** film **(RazorPagesMovie.Models)** aşağı, seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="cc315-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc315-162">Veri **bağlamında sınıf** satırında, yeni sınıfın adını yazın, RazorPagesMovie. **Veri**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cc315-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cc315-163">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="cc315-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cc315-164">Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cc315-165">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-165">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

<span data-ttu-id="cc315-167">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="cc315-168">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="cc315-168">Add EF tools</span></span>

<span data-ttu-id="cc315-169">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cc315-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="cc315-170">Önceki komut .NET Core CLI için Entity Framework Core Tools ekler.</span><span class="sxs-lookup"><span data-stu-id="cc315-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="cc315-171">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="cc315-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-173">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="cc315-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cc315-174">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="cc315-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc315-175">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cc315-176">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="cc315-176">Updated</span></span>

* <span data-ttu-id="cc315-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-177">*Startup.cs*</span></span>

<span data-ttu-id="cc315-178">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-179">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc315-180">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="cc315-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cc315-181">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="cc315-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc315-182">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cc315-183">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="cc315-183">Updated</span></span>

* <span data-ttu-id="cc315-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-184">*Startup.cs*</span></span>

<span data-ttu-id="cc315-185">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc315-187">İskele işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="cc315-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="cc315-188">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="cc315-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="cc315-189">Oluşturulan dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cc315-190">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="cc315-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-192">Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cc315-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cc315-193">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-193">Add an initial migration.</span></span>
* <span data-ttu-id="cc315-194">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cc315-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="cc315-195">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cc315-197">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="cc315-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="cc315-200">Önceki komutlar şu uyarıyı oluşturur: "Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="cc315-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="cc315-201">Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="cc315-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="cc315-202">'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin."</span><span class="sxs-lookup"><span data-stu-id="cc315-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="cc315-203">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="cc315-204">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="cc315-205">Şema, 'de `DbContext`belirtilen modele dayanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="cc315-206">Bağımsız `InitialCreate` değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="cc315-207">Herhangi bir ad kullanılabilir, ancak kural olarak geçişi açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="cc315-208">Komut, `update` uygulanmamış geçişlerde `Up` yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="cc315-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="cc315-209">Bu durumda, `update` veritabanını `Up` oluşturan *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntem çalışır.</span><span class="sxs-lookup"><span data-stu-id="cc315-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cc315-211">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="cc315-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cc315-212">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cc315-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc315-213">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cc315-214">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cc315-215">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cc315-216">İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="cc315-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cc315-217">`Startup.ConfigureServices` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cc315-218">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="cc315-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="cc315-219">Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie`</span><span class="sxs-lookup"><span data-stu-id="cc315-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cc315-220">Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cc315-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cc315-221">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="cc315-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cc315-222">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cc315-223">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="cc315-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cc315-224">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="cc315-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cc315-225">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cc315-226">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="cc315-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc315-228">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc315-230">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cc315-231">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="cc315-231">Test the app</span></span>

* <span data-ttu-id="cc315-232">Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).</span><span class="sxs-lookup"><span data-stu-id="cc315-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cc315-233">Hata alırsanız:</span><span class="sxs-lookup"><span data-stu-id="cc315-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cc315-234">[Göçler adımını](#pmc)kaçırdın.</span><span class="sxs-lookup"><span data-stu-id="cc315-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cc315-235">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="cc315-235">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cc315-237">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cc315-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cc315-238">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cc315-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cc315-239">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="cc315-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cc315-240">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="cc315-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cc315-241">Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.</span><span class="sxs-lookup"><span data-stu-id="cc315-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc315-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cc315-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cc315-243">[Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cc315-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc315-244">Bu bölümde, bir çapraz platform [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetmek için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="cc315-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="cc315-245">ASP.NET Core şablonundan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="cc315-246">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="cc315-247">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisieşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="cc315-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="cc315-248">MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur.</span><span class="sxs-lookup"><span data-stu-id="cc315-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cc315-249">Veritabanında depolanan verilerin özelliklerini tanımlarlar.</span><span class="sxs-lookup"><span data-stu-id="cc315-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cc315-250">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="cc315-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-252">**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** > </span><span class="sxs-lookup"><span data-stu-id="cc315-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cc315-253">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cc315-253">Name the folder *Models*.</span></span>

<span data-ttu-id="cc315-254">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-254">Right click the *Models* folder.</span></span> <span data-ttu-id="cc315-255">**Sınıf** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="cc315-256">Sınıf **Film**adı .</span><span class="sxs-lookup"><span data-stu-id="cc315-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cc315-258">*Modeller*adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cc315-259">*Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cc315-261">Solution Explorer'da **RazorPagesMovie** projesini sağ tıklatın ve ardından**Yeni Klasör** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cc315-262">*Klasör*Modelleri'ni adlandırın.</span><span class="sxs-lookup"><span data-stu-id="cc315-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="cc315-263">*Modeller* klasörüne sağ tıklayın ve ardından **Yeni Dosya** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cc315-264">Yeni **Dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="cc315-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cc315-265">Sol bölmede **Genel'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cc315-266">Orta bölmede **Boş Sınıf'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cc315-267">Sınıf **Film** adını ve **Yeni**seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cc315-268">Derleme hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="cc315-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cc315-269">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="cc315-269">Scaffold the movie model</span></span>

<span data-ttu-id="cc315-270">Bu bölümde, film modeli iskele.</span><span class="sxs-lookup"><span data-stu-id="cc315-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cc315-271">Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-273">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cc315-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc315-274">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc315-275">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="cc315-275">Name the folder *Movies*</span></span>

<span data-ttu-id="cc315-276">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="cc315-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/sca.png)

<span data-ttu-id="cc315-278">İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**</span><span class="sxs-lookup"><span data-stu-id="cc315-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="cc315-280">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="cc315-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="cc315-281">Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="cc315-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc315-282">Veri **bağlamında sınıf** satırında(artı) işaretini **+** seçin ve oluşturulan **razorPagesMovie.Models.RazorPagesMovieContext**adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="cc315-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cc315-283">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-283">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arp.png)

<span data-ttu-id="cc315-285">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cc315-287">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="cc315-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="cc315-288">**Windows için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cc315-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cc315-289">**macOS ve Linux için**: Aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="cc315-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-290">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc315-291">*Sayfalar/Filmler* klasörü oluşturun:</span><span class="sxs-lookup"><span data-stu-id="cc315-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc315-292">**Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="cc315-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc315-293">*Klasörü Filmler'e* adlandır</span><span class="sxs-lookup"><span data-stu-id="cc315-293">Name the folder *Movies*</span></span>

<span data-ttu-id="cc315-294">*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** ></span><span class="sxs-lookup"><span data-stu-id="cc315-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

<span data-ttu-id="cc315-296">Yeni **İskele Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **Ekle'yi**kullanarak Jilet Sayfaları'nı seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cc315-298">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="cc315-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc315-299">Model **sınıfında** aşağı açılan, **Film**seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="cc315-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="cc315-300">Veri **bağlamı sınıf** satırında, **RazorPagesMovieContext'ı** yazın, bu da doğru ad alanına sahip yeni bir db bağlam sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="cc315-301">Bu durumda **RazorPagesMovie.Models.RazorPagesMovieContext**olacak .</span><span class="sxs-lookup"><span data-stu-id="cc315-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cc315-302">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-302">Select **Add**.</span></span>

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

<span data-ttu-id="cc315-304">*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="cc315-305">İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="cc315-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="cc315-306">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="cc315-306">Files created</span></span>

* <span data-ttu-id="cc315-307">*Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.</span><span class="sxs-lookup"><span data-stu-id="cc315-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc315-308">*Veri/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="cc315-309">Dosya güncellendi</span><span class="sxs-lookup"><span data-stu-id="cc315-309">File updated</span></span>

* <span data-ttu-id="cc315-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc315-310">*Startup.cs*</span></span>

<span data-ttu-id="cc315-311">Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cc315-312">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="cc315-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc315-314">Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="cc315-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cc315-315">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-315">Add an initial migration.</span></span>
* <span data-ttu-id="cc315-316">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="cc315-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="cc315-317">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="cc315-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cc315-319">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="cc315-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="cc315-320">Komut, `Add-Migration` ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cc315-321">Şema,(RazorPagesMovieContext.cs dosyasında) belirtilen modele dayanır. *RazorPagesMovieContext.cs* `DbContext`</span><span class="sxs-lookup"><span data-stu-id="cc315-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cc315-322">Bağımsız `InitialCreate` değişken, geçişin adını vermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="cc315-323">Herhangi bir ad kullanılabilir, ancak kuralıile geçiş açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="cc315-324">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="cc315-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="cc315-325">Komut, `Update-Database` `Up` *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="cc315-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="cc315-326">Yöntem `Up` veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-328">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="cc315-329">Önceki komutlar şu uyarıyı oluşturur: "*Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi. Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur. 'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin.* Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc315-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cc315-331">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="cc315-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cc315-332">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cc315-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc315-333">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cc315-334">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="cc315-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cc315-335">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="cc315-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cc315-336">İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="cc315-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cc315-337">`Startup.ConfigureServices` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cc315-338">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="cc315-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="cc315-339">Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie`</span><span class="sxs-lookup"><span data-stu-id="cc315-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cc315-340">Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cc315-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cc315-341">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="cc315-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cc315-342">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="cc315-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cc315-343">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="cc315-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cc315-344">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="cc315-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cc315-345">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="cc315-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cc315-346">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="cc315-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc315-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc315-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc315-348">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc315-349">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc315-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc315-350">`Up` Yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="cc315-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cc315-351">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="cc315-351">Test the app</span></span>

* <span data-ttu-id="cc315-352">Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).</span><span class="sxs-lookup"><span data-stu-id="cc315-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cc315-353">Hata alırsanız:</span><span class="sxs-lookup"><span data-stu-id="cc315-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cc315-354">[Göçler adımını](#pmc)kaçırdın.</span><span class="sxs-lookup"><span data-stu-id="cc315-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cc315-355">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="cc315-355">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cc315-357">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cc315-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cc315-358">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cc315-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cc315-359">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="cc315-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cc315-360">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="cc315-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cc315-361">Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.</span><span class="sxs-lookup"><span data-stu-id="cc315-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc315-362">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cc315-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cc315-363">[Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cc315-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
