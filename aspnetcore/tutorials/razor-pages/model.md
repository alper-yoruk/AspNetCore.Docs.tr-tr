---
title: Bölüm 2, model ekleme
author: rick-anderson
description: Sayfalardaki eğitim serisinin 2. bölümü Razor . Bu bölümde, model sınıfları eklenir.
ms.author: riande
ms.date: 11/11/2020
no-loc:
- Index
- appsettings.json
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
ms.openlocfilehash: b2e840e20d034b42b2dc4a525b1dd76e44bbe3a8
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420064"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="03fec-104">Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="03fec-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="03fec-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03fec-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="03fec-106">Bu bölümde, bir veritabanında film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="03fec-107">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="03fec-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="03fec-108">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="03fec-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="03fec-109">Önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="03fec-110">Model sınıfları, EF Core bağımlılığı olmadığından POCO sınıfları ("**P** Lain-**O** ld **C** LR **o** bjects") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="03fec-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="03fec-111">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="03fec-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="03fec-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="03fec-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="03fec-113">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="03fec-115">**Çözüm Gezgini**, yeni klasör ekle > *Razor pagesmovie* projesine sağ tıklayın **Add**  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="03fec-116">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="03fec-117">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="03fec-118">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="03fec-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="03fec-119">Sınıf *filmi* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="03fec-120">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-121">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-122">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-123">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-124">With this attribute:</span></span>

  * <span data-ttu-id="03fec-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="03fec-128">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="03fec-129">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="03fec-130">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-131">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-132">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-133">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-134">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-134">With this attribute:</span></span>

  * <span data-ttu-id="03fec-135">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-136">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="03fec-137">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="03fec-138">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-138">Add a database context class</span></span>

1. <span data-ttu-id="03fec-139">*Razor Pagesmovie* projesinde, *Data* adlı bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="03fec-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="03fec-140">*Veri* klasörüne aşağıdaki kodla *Razor PagesMovieContext.cs* adlı bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="03fec-141">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="03fec-142">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="03fec-143">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="03fec-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="03fec-144">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="03fec-144">Add a database connection string</span></span>

<span data-ttu-id="03fec-145">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="03fec-146">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="03fec-146">Register the database context</span></span>

1. <span data-ttu-id="03fec-147">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="03fec-148">Veritabanı bağlamını, içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="03fec-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-149">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="03fec-150">**Çözüm aracı penceresinde**, *Razor pagesmovie* projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="03fec-151">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="03fec-152">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="03fec-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="03fec-153">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="03fec-154">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="03fec-155">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="03fec-156">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-157">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-158">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-159">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-160">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-160">With this attribute:</span></span>

  * <span data-ttu-id="03fec-161">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-162">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="03fec-163">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="03fec-164">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="03fec-165">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="03fec-165">Scaffold the movie model</span></span>

<span data-ttu-id="03fec-166">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="03fec-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="03fec-167">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="03fec-169">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="03fec-170">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="03fec-171">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="03fec-172">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/sca.png)

1. <span data-ttu-id="03fec-174">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="03fec-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

1. <span data-ttu-id="03fec-176">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="03fec-177">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="03fec-178">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="03fec-179">**Veri bağlamı Ekle** iletişim kutusunda, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext* oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="03fec-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="03fec-180">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-180">Select **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="03fec-182">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="03fec-184">*Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizinine bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="03fec-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="03fec-185">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="03fec-186">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="03fec-187">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="03fec-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="03fec-188">Seçenek</span><span class="sxs-lookup"><span data-stu-id="03fec-188">Option</span></span>               | <span data-ttu-id="03fec-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="03fec-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="03fec-190">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="03fec-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="03fec-191">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="03fec-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="03fec-192">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="03fec-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="03fec-193">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="03fec-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="03fec-194">`_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler</span><span class="sxs-lookup"><span data-stu-id="03fec-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="03fec-195">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="03fec-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="03fec-196">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="03fec-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="03fec-197">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="03fec-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="03fec-198">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="03fec-199">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="03fec-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="03fec-200">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-201">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="03fec-202">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="03fec-203">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="03fec-204">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="03fec-205">*Pages/Movies* **Add** > **Yeni yapı iskelesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

1. <span data-ttu-id="03fec-207">**Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="03fec-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="03fec-209">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="03fec-210">**Kullanılacak DbContext sınıfında:** Row, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="03fec-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="03fec-211">**Son**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-211">Select **Finish**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/arpMac.png)

<span data-ttu-id="03fec-213">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="03fec-214">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="03fec-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="03fec-215">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="03fec-216">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="03fec-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="03fec-217">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="03fec-218">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="03fec-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-220">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="03fec-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="03fec-221">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="03fec-222">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="03fec-223">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="03fec-223">Updated</span></span>

* <span data-ttu-id="03fec-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-224">*Startup.cs*</span></span>

<span data-ttu-id="03fec-225">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03fec-227">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="03fec-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="03fec-228">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="03fec-229">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-230">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03fec-231">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="03fec-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="03fec-232">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="03fec-233">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="03fec-234">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="03fec-234">Updated</span></span>

* <span data-ttu-id="03fec-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-235">*Startup.cs*</span></span>

<span data-ttu-id="03fec-236">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="03fec-237">EF 'in geçiş özelliğini kullanarak ilk veritabanı şemasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="03fec-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="03fec-238">Entity Framework Core geçiş özelliği şunları yapmak için bir yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="03fec-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="03fec-239">İlk veritabanı şemasını oluşturun.</span><span class="sxs-lookup"><span data-stu-id="03fec-239">Create the initial database schema.</span></span>
* <span data-ttu-id="03fec-240">Veritabanı şemasını uygulamanın veri modeliyle eşitlenmiş halde tutmak için artımlı olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="03fec-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="03fec-241">Veritabanındaki mevcut veriler korunur.</span><span class="sxs-lookup"><span data-stu-id="03fec-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-243">Bu bölümde, için **Paket Yöneticisi konsolu** (PMC) penceresi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="03fec-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="03fec-244">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-244">Add an initial migration.</span></span>
* <span data-ttu-id="03fec-245">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="03fec-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="03fec-246">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC menüsü](model/_static/5/pmc.png)

1. <span data-ttu-id="03fec-248">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="03fec-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-249">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="03fec-250">Aşağıdaki .NET CLı komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="03fec-251">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="03fec-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="03fec-252">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="03fec-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="03fec-253">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="03fec-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="03fec-254">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="03fec-255">`migrations`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="03fec-256">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="03fec-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="03fec-257">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="03fec-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="03fec-258">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="03fec-259">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="03fec-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="03fec-260">Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="03fec-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="03fec-262">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="03fec-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="03fec-263">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="03fec-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="03fec-264">EF Core veritabanı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="03fec-265">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="03fec-266">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="03fec-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="03fec-267">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="03fec-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="03fec-268">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03fec-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="03fec-269">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="03fec-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="03fec-270">`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` .</span><span class="sxs-lookup"><span data-stu-id="03fec-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="03fec-271">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="03fec-272">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="03fec-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="03fec-273">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="03fec-274">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="03fec-275">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="03fec-276">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="03fec-277">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="03fec-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-278">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="03fec-279">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="03fec-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="03fec-280">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="03fec-280">Test the app</span></span>

1. <span data-ttu-id="03fec-281">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="03fec-282">Aşağıdaki hata iletisini alırsanız:</span><span class="sxs-lookup"><span data-stu-id="03fec-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="03fec-283">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="03fec-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="03fec-284">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-284">Test the **Create** link.</span></span>

   ![Sayfa oluşturma](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="03fec-286">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="03fec-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="03fec-287">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="03fec-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="03fec-288">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="03fec-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="03fec-289">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="03fec-290">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03fec-291">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="03fec-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="03fec-292">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="03fec-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="03fec-293">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="03fec-294">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="03fec-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="03fec-295">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="03fec-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="03fec-296">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="03fec-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="03fec-297">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="03fec-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="03fec-298">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="03fec-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="03fec-299">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-301">Yeni klasör **eklemek**> **Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="03fec-302">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-302">Name the folder *Models*.</span></span>

<span data-ttu-id="03fec-303">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="03fec-304">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="03fec-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="03fec-305">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-305">Name the class **Movie**.</span></span>

<span data-ttu-id="03fec-306">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-307">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-308">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-309">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-310">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-310">With this attribute:</span></span>

  * <span data-ttu-id="03fec-311">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-312">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="03fec-313">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="03fec-315">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="03fec-316">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="03fec-317">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-318">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-319">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-320">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-321">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-321">With this attribute:</span></span>

  * <span data-ttu-id="03fec-322">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-323">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="03fec-324">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="03fec-325">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="03fec-326">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-326">Add a database context class</span></span>

* <span data-ttu-id="03fec-327">*Razor Pagesmovie* projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="03fec-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="03fec-328">Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="03fec-329">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="03fec-330">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="03fec-331">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="03fec-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="03fec-332">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="03fec-332">Add a database connection string</span></span>

<span data-ttu-id="03fec-333">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="03fec-334">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="03fec-334">Register the database context</span></span>

<span data-ttu-id="03fec-335">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="03fec-336">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03fec-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-337">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="03fec-338">**Çözüm aracı penceresinde**, **Razor pagesmovie** projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="03fec-339">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="03fec-340">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="03fec-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="03fec-341">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="03fec-342">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="03fec-343">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="03fec-344">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-345">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-346">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-347">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-348">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-348">With this attribute:</span></span>

  * <span data-ttu-id="03fec-349">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-350">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="03fec-351">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="03fec-352">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="03fec-353">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="03fec-353">Scaffold the movie model</span></span>

<span data-ttu-id="03fec-354">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="03fec-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="03fec-355">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-357">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="03fec-358">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="03fec-359">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="03fec-360">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="03fec-362">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="03fec-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="03fec-364">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="03fec-365">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="03fec-366">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı Razor pagesmovie 'den değiştirin.**Modeller**. Razor Pagesmovie 'e PagesMovieContext Razor .**Veri**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="03fec-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="03fec-367">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="03fec-368">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="03fec-369">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-369">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="03fec-371">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="03fec-373">*Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="03fec-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="03fec-374">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="03fec-375">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="03fec-376">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="03fec-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="03fec-377">Seçenek</span><span class="sxs-lookup"><span data-stu-id="03fec-377">Option</span></span>               | <span data-ttu-id="03fec-378">Açıklama</span><span class="sxs-lookup"><span data-stu-id="03fec-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="03fec-379">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="03fec-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="03fec-380">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="03fec-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="03fec-381">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="03fec-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="03fec-382">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="03fec-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="03fec-383">`_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler</span><span class="sxs-lookup"><span data-stu-id="03fec-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="03fec-384">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="03fec-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="03fec-385">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="03fec-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="03fec-386">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="03fec-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="03fec-387">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="03fec-388">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="03fec-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="03fec-389">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-390">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03fec-391">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="03fec-392">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="03fec-393">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="03fec-394">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.</span><span class="sxs-lookup"><span data-stu-id="03fec-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="03fec-396">**Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="03fec-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="03fec-398">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="03fec-399">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( Razor pagesmovie. modeller)**.</span><span class="sxs-lookup"><span data-stu-id="03fec-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="03fec-400">**Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, Razor pagesfilmi.**Veri**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="03fec-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="03fec-401">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="03fec-402">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="03fec-403">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-403">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="03fec-405">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="03fec-406">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-406">Add EF tools</span></span>

<span data-ttu-id="03fec-407">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="03fec-408">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="03fec-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="03fec-409">Daha fazla bilgi için bkz. [Entity Framework Core araçları başvurusu-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="03fec-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="03fec-410">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="03fec-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="03fec-411">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="03fec-412">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="03fec-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="03fec-413">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="03fec-414">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="03fec-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-416">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="03fec-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="03fec-417">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="03fec-418">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="03fec-419">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="03fec-419">Updated</span></span>

* <span data-ttu-id="03fec-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-420">*Startup.cs*</span></span>

<span data-ttu-id="03fec-421">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-422">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03fec-423">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="03fec-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="03fec-424">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="03fec-425">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="03fec-426">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="03fec-426">Updated</span></span>

* <span data-ttu-id="03fec-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-427">*Startup.cs*</span></span>

<span data-ttu-id="03fec-428">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="03fec-430">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="03fec-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="03fec-431">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="03fec-432">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="03fec-433">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="03fec-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-435">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="03fec-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="03fec-436">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-436">Add an initial migration.</span></span>
* <span data-ttu-id="03fec-437">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="03fec-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="03fec-438">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="03fec-440">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="03fec-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-441">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="03fec-442">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="03fec-443">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="03fec-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="03fec-444">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="03fec-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="03fec-445">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="03fec-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="03fec-446">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="03fec-447">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="03fec-448">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="03fec-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="03fec-449">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="03fec-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="03fec-450">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="03fec-451">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="03fec-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="03fec-452">Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="03fec-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="03fec-454">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="03fec-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="03fec-455">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="03fec-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="03fec-456">EF Core veritabanı bağlamı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="03fec-457">Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="03fec-458">Bir veritabanı bağlamı bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="03fec-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="03fec-459">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="03fec-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="03fec-460">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03fec-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="03fec-461">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="03fec-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="03fec-462">`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` .</span><span class="sxs-lookup"><span data-stu-id="03fec-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="03fec-463">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="03fec-464">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="03fec-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="03fec-465">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="03fec-466">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="03fec-467">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="03fec-468">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="03fec-469">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="03fec-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-470">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="03fec-471">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="03fec-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="03fec-472">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="03fec-472">Test the app</span></span>

* <span data-ttu-id="03fec-473">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="03fec-474">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="03fec-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="03fec-475">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="03fec-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="03fec-476">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-476">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="03fec-478">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="03fec-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="03fec-479">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="03fec-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="03fec-480">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="03fec-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="03fec-481">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="03fec-482">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03fec-483">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="03fec-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="03fec-484">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="03fec-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="03fec-485">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="03fec-486">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="03fec-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="03fec-487">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="03fec-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="03fec-488">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="03fec-489">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="03fec-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="03fec-490">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="03fec-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="03fec-491">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="03fec-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="03fec-492">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-494">Yeni klasör **eklemek**> **Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="03fec-495">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-495">Name the folder *Models*.</span></span>

<span data-ttu-id="03fec-496">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="03fec-497">Sınıf **Ekle**' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="03fec-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="03fec-498">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-498">Name the class **Movie**.</span></span>

<span data-ttu-id="03fec-499">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-500">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-501">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-502">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-503">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-503">With this attribute:</span></span>

  * <span data-ttu-id="03fec-504">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-505">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="03fec-506">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="03fec-508">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="03fec-509">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="03fec-510">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-511">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-512">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-513">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-514">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-514">With this attribute:</span></span>

  * <span data-ttu-id="03fec-515">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-516">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="03fec-517">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="03fec-518">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="03fec-519">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="03fec-519">Add a database context class</span></span>

<span data-ttu-id="03fec-520">RazorPagesmovie projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="03fec-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="03fec-521">Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="03fec-522">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="03fec-523">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="03fec-524">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="03fec-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="03fec-525">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="03fec-525">Add a database connection string</span></span>

<span data-ttu-id="03fec-526">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="03fec-527">Gerekli NuGet paketlerini ekleyin</span><span class="sxs-lookup"><span data-stu-id="03fec-527">Add required NuGet packages</span></span>

<span data-ttu-id="03fec-528">SQLite ve CodeGeneration eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın. projeye tasarım:</span><span class="sxs-lookup"><span data-stu-id="03fec-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="03fec-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Paket, yapı iskelesi için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="03fec-530">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="03fec-530">Register the database context</span></span>

<span data-ttu-id="03fec-531">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="03fec-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="03fec-532">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03fec-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="03fec-533">Hataları denetlemek için projeyi bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-534">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="03fec-535">**Çözüm aracı penceresinde**, *Razor pagesmovie* projesine denetim yapın ve ardından **Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="03fec-536">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="03fec-537">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="03fec-538">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="03fec-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="03fec-539">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="03fec-540">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="03fec-541">Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="03fec-542">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="03fec-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="03fec-543">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="03fec-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="03fec-544">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="03fec-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="03fec-545">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="03fec-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="03fec-546">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="03fec-546">With this attribute:</span></span>

  * <span data-ttu-id="03fec-547">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="03fec-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="03fec-548">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="03fec-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="03fec-549">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="03fec-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="03fec-550">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="03fec-551">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="03fec-551">Scaffold the movie model</span></span>

<span data-ttu-id="03fec-552">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="03fec-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="03fec-553">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-555">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="03fec-556">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="03fec-557">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="03fec-558">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="03fec-560">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="03fec-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="03fec-562">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="03fec-563">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="03fec-564">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **Razor pagesmovie. modeller adlı adı kabul edin. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="03fec-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="03fec-565">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-565">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="03fec-567">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="03fec-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="03fec-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="03fec-569">*Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="03fec-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="03fec-570">**Windows için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="03fec-571">**MacOS ve Linux için**: aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="03fec-572">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="03fec-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="03fec-573">Seçenek</span><span class="sxs-lookup"><span data-stu-id="03fec-573">Option</span></span>               | <span data-ttu-id="03fec-574">Açıklama</span><span class="sxs-lookup"><span data-stu-id="03fec-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="03fec-575">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="03fec-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="03fec-576">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="03fec-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="03fec-577">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="03fec-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="03fec-578">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="03fec-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="03fec-579">`_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler</span><span class="sxs-lookup"><span data-stu-id="03fec-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="03fec-580">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="03fec-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="03fec-581">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="03fec-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="03fec-582">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="03fec-583">*Sayfalar/filmler* klasörü oluştur:</span><span class="sxs-lookup"><span data-stu-id="03fec-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="03fec-584">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="03fec-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="03fec-585">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="03fec-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="03fec-586">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="03fec-588">**Yeni yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="03fec-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="03fec-590">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="03fec-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="03fec-591">**Model sınıfı** açılan kutusunda **film**' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="03fec-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="03fec-592">**Veri bağlamı sınıfı** satırında, **Razor pagesmoviecontext** ' i seçin. Bu, doğru ad alanıyla yeni bir veritabanı bağlamı sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="03fec-593">Bu durumda, **Razor pagesmovie. modeller olacaktır. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="03fec-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="03fec-594">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-594">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="03fec-596">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="03fec-597">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="03fec-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="03fec-598">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="03fec-598">Files created</span></span>

* <span data-ttu-id="03fec-599">*Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .</span><span class="sxs-lookup"><span data-stu-id="03fec-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="03fec-600">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="03fec-601">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="03fec-601">File updated</span></span>

* <span data-ttu-id="03fec-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="03fec-602">*Startup.cs*</span></span>

<span data-ttu-id="03fec-603">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="03fec-604">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="03fec-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="03fec-606">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="03fec-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="03fec-607">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-607">Add an initial migration.</span></span>
* <span data-ttu-id="03fec-608">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="03fec-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="03fec-609">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="03fec-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="03fec-611">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="03fec-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="03fec-612">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="03fec-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="03fec-613">Şema, `DbContext` *Razor PagesMovieContext.cs* dosyasında belirtilen modeli temel alır.</span><span class="sxs-lookup"><span data-stu-id="03fec-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="03fec-614">`InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="03fec-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="03fec-615">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="03fec-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="03fec-616">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="03fec-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="03fec-617">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="03fec-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="03fec-618">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-619">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="03fec-620">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="03fec-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="03fec-621">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="03fec-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="03fec-622">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="03fec-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="03fec-623">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="03fec-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="03fec-624">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="03fec-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="03fec-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="03fec-626">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="03fec-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="03fec-627">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="03fec-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="03fec-628">Hizmetler (EF Core veritabanı bağlamı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="03fec-629">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="03fec-630">Bir veritabanı bağlamı contextB bağlam örneğini alan Oluşturucu kodu, daha sonra öğreticide gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="03fec-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="03fec-631">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="03fec-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="03fec-632">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="03fec-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="03fec-633">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="03fec-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="03fec-634">`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` .</span><span class="sxs-lookup"><span data-stu-id="03fec-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="03fec-635">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="03fec-636">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="03fec-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="03fec-637">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="03fec-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="03fec-638">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="03fec-639">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="03fec-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="03fec-640">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="03fec-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="03fec-641">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="03fec-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="03fec-642">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="03fec-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="03fec-643">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="03fec-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="03fec-644">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="03fec-644">Test the app</span></span>

* <span data-ttu-id="03fec-645">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="03fec-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="03fec-646">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="03fec-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="03fec-647">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="03fec-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="03fec-648">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-648">Test the **Create** link.</span></span>

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="03fec-650">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="03fec-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="03fec-651">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="03fec-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="03fec-652">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="03fec-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="03fec-653">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="03fec-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="03fec-654">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="03fec-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03fec-655">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="03fec-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="03fec-656">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="03fec-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
