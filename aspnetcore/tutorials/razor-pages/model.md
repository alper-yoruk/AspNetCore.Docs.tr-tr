---
title: 'Bölüm 2, :::no-loc(Razor)::: ASP.NET Core bir sayfalar uygulamasına bir model ekleyin'
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 2. bölümü :::no-loc(Razor)::: .'
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050933"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="1e428-103">Bölüm 2, :::no-loc(Razor)::: ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="1e428-103">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="1e428-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e428-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="1e428-105">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e428-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="1e428-106">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="1e428-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="1e428-107">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="1e428-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="1e428-108">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="1e428-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1e428-109">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1e428-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1e428-110">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1e428-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-112">Yeni klasör **eklemek** > **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın  >  **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-112">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder** .</span></span> <span data-ttu-id="1e428-113">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-113">Name the folder *Models* .</span></span>

<span data-ttu-id="1e428-114">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1e428-114">Right click the *Models* folder.</span></span> <span data-ttu-id="1e428-115">Sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="1e428-115">Select **Add** > **Class** .</span></span> <span data-ttu-id="1e428-116">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-116">Name the class **Movie** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e428-118">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-118">Add a folder named *Models* .</span></span>
* <span data-ttu-id="1e428-119">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-119">Add a class to the *Models* folder named *Movie.cs* .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-120">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e428-121">Çözüm bölmesi, **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-121">In Solution Pad, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...** . Name the folder *Models* .</span></span>
* <span data-ttu-id="1e428-122">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-122">Right-click the *Models* folder, and then select **Add** > **New File...** .</span></span>
* <span data-ttu-id="1e428-123">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="1e428-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1e428-124">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1e428-125">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1e428-126">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-126">Name the class **Movie** and select **New** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1e428-127">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1e428-128">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-128">Scaffold the movie model</span></span>

<span data-ttu-id="1e428-129">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="1e428-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1e428-130">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="1e428-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-132">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="1e428-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1e428-133">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-133">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="1e428-134">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-134">Name the folder *Movies*</span></span>

<span data-ttu-id="1e428-135">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1e428-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="1e428-137">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add** .</span><span class="sxs-lookup"><span data-stu-id="1e428-137">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="1e428-139">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="1e428-139">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1e428-140">**Model sınıfı** açılan kutusunda **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-140">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="1e428-141">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı :::no-loc(Razor)::: pagesmovie 'den değiştirin. **Modeller** . :::no-loc(Razor)::: Pagesmovie 'e PagesMovieContext :::no-loc(Razor)::: . **Veri** . :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="1e428-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models** .:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data** .:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="1e428-142">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1e428-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1e428-143">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e428-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1e428-144">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-144">Select **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="1e428-146">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-146">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="1e428-148">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="1e428-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="1e428-149">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="1e428-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="1e428-150">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e428-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1e428-151">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e428-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-152">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e428-153">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="1e428-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1e428-154">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-154">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="1e428-155">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-155">Name the folder *Movies*</span></span>

<span data-ttu-id="1e428-156">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...** .</span><span class="sxs-lookup"><span data-stu-id="1e428-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="1e428-158">**Yeni yapı iskelesi** iletişim kutusunda, İleri **:::no-loc(Razor)::: Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next** .</span><span class="sxs-lookup"><span data-stu-id="1e428-158">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1e428-160">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="1e428-160">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1e428-161">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** .</span><span class="sxs-lookup"><span data-stu-id="1e428-161">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="1e428-162">**Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, :::no-loc(Razor)::: pagesfilmi. **Veri** . :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="1e428-162">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data** .:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="1e428-163">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="1e428-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="1e428-164">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e428-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="1e428-165">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-165">Select **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="1e428-167">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-167">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="1e428-168">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="1e428-168">Add EF tools</span></span>

<span data-ttu-id="1e428-169">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e428-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="1e428-170">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="1e428-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="1e428-171">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="1e428-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-173">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="1e428-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1e428-174">*Sayfalar/filmler* : oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="1e428-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1e428-175">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-175">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1e428-176">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="1e428-176">Updated</span></span>

* <span data-ttu-id="1e428-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-177">*Startup.cs*</span></span>

<span data-ttu-id="1e428-178">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-179">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e428-180">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="1e428-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="1e428-181">*Sayfalar/filmler* : oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="1e428-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1e428-182">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-182">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="1e428-183">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="1e428-183">Updated</span></span>

* <span data-ttu-id="1e428-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-184">*Startup.cs*</span></span>

<span data-ttu-id="1e428-185">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e428-187">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1e428-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="1e428-188">*Sayfalar/filmler* : oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="1e428-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="1e428-189">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1e428-190">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="1e428-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-192">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e428-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1e428-193">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-193">Add an initial migration.</span></span>
* <span data-ttu-id="1e428-194">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="1e428-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="1e428-195">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** .</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1e428-197">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="1e428-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-199">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="1e428-200">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="1e428-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="1e428-201">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="1e428-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="1e428-202">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="1e428-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="1e428-203">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="1e428-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="1e428-204">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="1e428-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="1e428-205">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="1e428-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="1e428-206">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e428-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="1e428-207">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="1e428-208">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="1e428-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="1e428-209">Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="1e428-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1e428-211">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="1e428-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1e428-212">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="1e428-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1e428-213">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1e428-214">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-214">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1e428-215">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1e428-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1e428-216">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="1e428-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1e428-217">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1e428-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1e428-218">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="1e428-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1e428-219">`:::no-loc(Razor):::PagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="1e428-219">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1e428-220">Veri bağlamı ( `:::no-loc(Razor):::PagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-220">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1e428-221">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1e428-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="1e428-222">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e428-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1e428-223">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1e428-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1e428-224">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1e428-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1e428-225">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1e428-226">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1e428-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e428-228">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="1e428-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-229">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e428-230">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="1e428-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1e428-231">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="1e428-231">Test the app</span></span>

* <span data-ttu-id="1e428-232">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1e428-233">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="1e428-233">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1e428-234">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="1e428-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1e428-235">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="1e428-235">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1e428-237">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="1e428-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1e428-238">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e428-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1e428-239">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="1e428-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1e428-240">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="1e428-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="1e428-241">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e428-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1e428-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1e428-243">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi :::no-loc(Razor)::: Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1e428-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e428-244">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e428-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="1e428-245">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="1e428-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="1e428-246">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e428-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="1e428-247">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="1e428-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="1e428-248">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="1e428-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1e428-249">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1e428-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="1e428-250">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="1e428-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-252">Yeni klasör **eklemek** > **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın  >  **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-252">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder** .</span></span> <span data-ttu-id="1e428-253">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-253">Name the folder *Models* .</span></span>

<span data-ttu-id="1e428-254">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1e428-254">Right click the *Models* folder.</span></span> <span data-ttu-id="1e428-255">Sınıf **Ekle** ' yi seçin  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="1e428-255">Select **Add** > **Class** .</span></span> <span data-ttu-id="1e428-256">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-256">Name the class **Movie** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1e428-258">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-258">Add a folder named *Models* .</span></span>
* <span data-ttu-id="1e428-259">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-259">Add a class to the *Models* folder named *Movie.cs* .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-260">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e428-261">Çözüm Gezgini, **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın ve ardından **Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-261">In Solution Explorer, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder** .</span></span> <span data-ttu-id="1e428-262">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1e428-262">Name the folder *Models* .</span></span>
* <span data-ttu-id="1e428-263">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-263">Right-click the *Models* folder, and then select **Add** > **New File** .</span></span>
* <span data-ttu-id="1e428-264">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="1e428-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1e428-265">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="1e428-266">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="1e428-267">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-267">Name the class **Movie** and select **New** .</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="1e428-268">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1e428-269">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-269">Scaffold the movie model</span></span>

<span data-ttu-id="1e428-270">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="1e428-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1e428-271">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="1e428-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-273">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="1e428-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1e428-274">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-274">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="1e428-275">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-275">Name the folder *Movies*</span></span>

<span data-ttu-id="1e428-276">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1e428-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="1e428-278">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add** .</span><span class="sxs-lookup"><span data-stu-id="1e428-278">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="1e428-280">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="1e428-280">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="1e428-281">**Model sınıfı** açılan kutusunda **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-281">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)** .</span></span>
* <span data-ttu-id="1e428-282">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **:::no-loc(Razor)::: pagesmovie. modeller adlı adı kabul edin. :::no-loc(Razor)::: PagesMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="1e428-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** .</span></span>
* <span data-ttu-id="1e428-283">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-283">Select **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="1e428-285">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-285">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="1e428-287">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="1e428-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="1e428-288">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e428-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="1e428-289">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1e428-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-290">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e428-291">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="1e428-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="1e428-292">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder** .</span><span class="sxs-lookup"><span data-stu-id="1e428-292">Right click on the *Pages* folder > **Add** > **New Folder** .</span></span>
* <span data-ttu-id="1e428-293">Klasör *filmlerini* adlandırın</span><span class="sxs-lookup"><span data-stu-id="1e428-293">Name the folder *Movies*</span></span>

<span data-ttu-id="1e428-294">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1e428-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="1e428-296">**Yeni yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add** .</span><span class="sxs-lookup"><span data-stu-id="1e428-296">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="1e428-298">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="1e428-298">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1e428-299">**Model sınıfı** açılan kutusunda **film** ' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="1e428-299">In the **Model class** drop down, select or type **Movie** .</span></span>
* <span data-ttu-id="1e428-300">**Veri bağlamı sınıfı** satırında, **:::no-loc(Razor)::: pagesmoviecontext** ' i seçin. Bu, doğru ad alanı ile yeni bir DB bağlam sınıfı oluşturacaktır.</span><span class="sxs-lookup"><span data-stu-id="1e428-300">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="1e428-301">Bu durumda, **:::no-loc(Razor)::: pagesmovie. modeller olacaktır. :::no-loc(Razor)::: PagesMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="1e428-301">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext** .</span></span>
* <span data-ttu-id="1e428-302">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-302">Select **Add** .</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="1e428-304">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-304">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="1e428-305">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="1e428-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1e428-306">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="1e428-306">Files created</span></span>

* <span data-ttu-id="1e428-307">*Sayfalar/filmler* : oluşturma, silme, ayrıntılar, düzenleme ve dizin.</span><span class="sxs-lookup"><span data-stu-id="1e428-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="1e428-308">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-308">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="1e428-309">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="1e428-309">File updated</span></span>

* <span data-ttu-id="1e428-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="1e428-310">*Startup.cs*</span></span>

<span data-ttu-id="1e428-311">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1e428-312">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="1e428-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e428-314">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e428-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="1e428-315">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-315">Add an initial migration.</span></span>
* <span data-ttu-id="1e428-316">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="1e428-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="1e428-317">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="1e428-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** .</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1e428-319">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="1e428-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="1e428-320">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="1e428-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="1e428-321">Şema, öğesinde belirtilen modeli temel alır `DbContext` ( *:::no-loc(Razor)::: PagesMovieContext.cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="1e428-321">The schema is based on the model specified in the `DbContext` (In the *:::no-loc(Razor):::PagesMovieContext.cs* file).</span></span> <span data-ttu-id="1e428-322">`InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e428-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="1e428-323">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e428-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="1e428-324">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="1e428-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="1e428-325">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="1e428-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="1e428-326">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e428-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-328">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="1e428-329">Yukarıdaki komutlar şu uyarıyı oluşturur: " *' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin.* " Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="1e428-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e428-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1e428-331">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="1e428-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1e428-332">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="1e428-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1e428-333">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1e428-334">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-334">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1e428-335">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1e428-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1e428-336">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="1e428-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1e428-337">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1e428-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1e428-338">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="1e428-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="1e428-339">`:::no-loc(Razor):::PagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="1e428-339">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1e428-340">Veri bağlamı ( `:::no-loc(Razor):::PagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-340">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1e428-341">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1e428-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="1e428-342">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e428-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1e428-343">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1e428-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1e428-344">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1e428-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1e428-345">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1e428-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1e428-346">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1e428-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e428-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e428-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e428-348">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="1e428-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e428-349">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e428-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e428-350">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="1e428-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1e428-351">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="1e428-351">Test the app</span></span>

* <span data-ttu-id="1e428-352">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e428-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1e428-353">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="1e428-353">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1e428-354">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="1e428-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1e428-355">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="1e428-355">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="1e428-357">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="1e428-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1e428-358">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e428-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1e428-359">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="1e428-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1e428-360">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="1e428-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="1e428-361">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e428-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e428-362">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1e428-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1e428-363">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi :::no-loc(Razor)::: Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="1e428-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
