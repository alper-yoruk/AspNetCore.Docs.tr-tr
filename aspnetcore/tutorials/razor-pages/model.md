---
title: Bölüm 2, model ekleme
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 2. bölümü Razor . Bu bölümde, model sınıfları eklenir.'
ms.author: riande
ms.date: 11/11/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550700"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="067da-104">Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="067da-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="067da-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="067da-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="067da-106">Bu bölümde, bir veritabanında film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="067da-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="067da-107">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="067da-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="067da-108">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="067da-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="067da-109">Önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="067da-110">Model sınıfları, EF Core bağımlılığı olmadığından POCO sınıfları (" **P** Lain- **O** ld **C** LR **o** bjects") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="067da-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="067da-111">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="067da-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="067da-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="067da-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="067da-113">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="067da-115">**Çözüm Gezgini** , yeni klasör ekle > *Razor pagesmovie* projesine sağ tıklayın **Add**  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-115">In **Solution Explorer** , right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="067da-116">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="067da-117">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="067da-118">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="067da-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="067da-119">Sınıf *filmi* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="067da-120">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-121">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-122">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-123">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-124">With this attribute:</span></span>

  * <span data-ttu-id="067da-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="067da-128">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="067da-129">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="067da-130">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-131">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-132">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-133">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-134">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-134">With this attribute:</span></span>

  * <span data-ttu-id="067da-135">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-136">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="067da-137">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="067da-138">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-138">Add a database context class</span></span>

1. <span data-ttu-id="067da-139">*Razor Pagesmovie* projesinde, *Data* adlı bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="067da-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="067da-140">*Veri* klasörüne aşağıdaki kodla *Razor PagesMovieContext.cs* adlı bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="067da-141">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="067da-142">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="067da-143">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="067da-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="067da-144">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="067da-144">Add a database connection string</span></span>

<span data-ttu-id="067da-145">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="067da-146">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="067da-146">Register the database context</span></span>

1. <span data-ttu-id="067da-147">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="067da-148">Veritabanı bağlamını, içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="067da-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-149">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="067da-150">**Çözüm aracı penceresinde** , *Razor pagesmovie* projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-150">In the **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="067da-151">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="067da-152">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="067da-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="067da-153">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="067da-154">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="067da-155">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="067da-156">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-157">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-158">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-159">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-160">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-160">With this attribute:</span></span>

  * <span data-ttu-id="067da-161">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-162">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="067da-163">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="067da-164">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="067da-165">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="067da-165">Scaffold the movie model</span></span>

<span data-ttu-id="067da-166">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="067da-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="067da-167">Diğer bir deyişle, scafkatlama aracı Create Film modeli için sayfa, okuma, güncelleştirme ve Delete (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="067da-169">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="067da-170">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="067da-171">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="067da-172">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/sca.png)

1. <span data-ttu-id="067da-174">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="067da-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

1. <span data-ttu-id="067da-176">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="067da-177">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="067da-178">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="067da-179">**Veri bağlamı Ekle** iletişim kutusunda, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext* oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="067da-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="067da-180">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-180">Select **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="067da-182">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="067da-184">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizinine bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="067da-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="067da-185">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="067da-186">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="067da-187">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="067da-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="067da-188">Seçenek</span><span class="sxs-lookup"><span data-stu-id="067da-188">Option</span></span>               | <span data-ttu-id="067da-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="067da-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="067da-190">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="067da-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="067da-191">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="067da-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="067da-192">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="067da-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="067da-193">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="067da-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="067da-194">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler Create</span><span class="sxs-lookup"><span data-stu-id="067da-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="067da-195">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="067da-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="067da-196">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="067da-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="067da-197">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="067da-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="067da-198">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="067da-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="067da-199">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="067da-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="067da-200">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="067da-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-201">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="067da-202">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="067da-203">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="067da-204">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="067da-205">*Pages/Movies* **Add** > **Yeni yapı iskelesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

1. <span data-ttu-id="067da-207">**Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="067da-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="067da-209">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="067da-210">**Kullanılacak DbContext sınıfında:** Row, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="067da-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="067da-211">**Son** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-211">Select **Finish**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/arpMac.png)

<span data-ttu-id="067da-213">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="067da-214">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="067da-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="067da-215">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="067da-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="067da-216">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="067da-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="067da-217">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="067da-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="067da-218">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="067da-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-220">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="067da-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="067da-221">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-221">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="067da-222">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="067da-223">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="067da-223">Updated</span></span>

* <span data-ttu-id="067da-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-224">*Startup.cs*</span></span>

<span data-ttu-id="067da-225">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="067da-227">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="067da-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="067da-228">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-228">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="067da-229">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-230">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067da-231">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="067da-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="067da-232">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-232">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="067da-233">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="067da-234">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="067da-234">Updated</span></span>

* <span data-ttu-id="067da-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-235">*Startup.cs*</span></span>

<span data-ttu-id="067da-236">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="067da-237">Create EF 'in geçiş özelliğini kullanan ilk veritabanı şeması</span><span class="sxs-lookup"><span data-stu-id="067da-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="067da-238">Entity Framework Core geçiş özelliği şunları yapmak için bir yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="067da-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="067da-239">Create İlk veritabanı şeması.</span><span class="sxs-lookup"><span data-stu-id="067da-239">Create the initial database schema.</span></span>
* <span data-ttu-id="067da-240">Veritabanı şemasını uygulamanın veri modeliyle eşitlenmiş halde tutmak için artımlı olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="067da-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="067da-241">Veritabanındaki mevcut veriler korunur.</span><span class="sxs-lookup"><span data-stu-id="067da-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-243">Bu bölümde, için **Paket Yöneticisi konsolu** (PMC) penceresi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="067da-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="067da-244">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-244">Add an initial migration.</span></span>
* <span data-ttu-id="067da-245">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="067da-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="067da-246">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC menüsü](model/_static/5/pmc.png)

1. <span data-ttu-id="067da-248">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="067da-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-249">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="067da-250">Aşağıdaki .NET CLı komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="067da-251">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="067da-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="067da-252">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="067da-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="067da-253">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="067da-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="067da-254">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="067da-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="067da-255">`migrations`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="067da-256">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="067da-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="067da-257">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="067da-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="067da-258">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="067da-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="067da-259">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="067da-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="067da-260">Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _Initial Create . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="067da-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="067da-262">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="067da-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="067da-263">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="067da-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="067da-264">EF Core veritabanı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="067da-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="067da-265">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="067da-266">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="067da-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="067da-267">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="067da-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="067da-268">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="067da-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="067da-269">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="067da-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="067da-270">`RazorPagesMovieContext`Koordinat, Create model için, okuma, güncelleştirme ve gibi işlevleri EF Core Delete `Movie` .</span><span class="sxs-lookup"><span data-stu-id="067da-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="067da-271">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="067da-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="067da-272">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="067da-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="067da-273">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="067da-274">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="067da-275">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="067da-276">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="067da-277">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="067da-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-278">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="067da-279">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="067da-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="067da-280">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="067da-280">Test the app</span></span>

1. <span data-ttu-id="067da-281">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="067da-282">Aşağıdaki hata iletisini alırsanız:</span><span class="sxs-lookup"><span data-stu-id="067da-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="067da-283">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="067da-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="067da-284">Bağlantıyı test edin **Create** .</span><span class="sxs-lookup"><span data-stu-id="067da-284">Test the **Create** link.</span></span>

   ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="067da-286">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="067da-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="067da-287">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="067da-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="067da-288">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="067da-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="067da-289">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="067da-289">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="067da-290">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="067da-291">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="067da-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="067da-292">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="067da-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="067da-293">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="067da-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="067da-294">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="067da-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="067da-295">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="067da-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="067da-296">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="067da-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="067da-297">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="067da-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="067da-298">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="067da-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="067da-299">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-301">Yeni klasör **eklemek** > **Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="067da-302">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-302">Name the folder *Models*.</span></span>

<span data-ttu-id="067da-303">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="067da-304">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="067da-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="067da-305">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-305">Name the class **Movie**.</span></span>

<span data-ttu-id="067da-306">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-307">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-308">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-309">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-310">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-310">With this attribute:</span></span>

  * <span data-ttu-id="067da-311">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-312">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="067da-313">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067da-315">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="067da-316">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="067da-317">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-318">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-319">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-320">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-321">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-321">With this attribute:</span></span>

  * <span data-ttu-id="067da-322">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-323">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="067da-324">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="067da-325">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="067da-326">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-326">Add a database context class</span></span>

* <span data-ttu-id="067da-327">*Razor Pagesmovie* projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="067da-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="067da-328">Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="067da-329">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="067da-330">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="067da-331">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="067da-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="067da-332">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="067da-332">Add a database connection string</span></span>

<span data-ttu-id="067da-333">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="067da-334">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="067da-334">Register the database context</span></span>

<span data-ttu-id="067da-335">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="067da-336">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="067da-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-337">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067da-338">**Çözüm aracı penceresinde** , **Razor pagesmovie** projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-338">In the **Solution Tool Window** , control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="067da-339">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="067da-340">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="067da-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="067da-341">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="067da-342">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="067da-343">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="067da-344">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-345">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-346">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-347">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-348">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-348">With this attribute:</span></span>

  * <span data-ttu-id="067da-349">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-350">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="067da-351">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="067da-352">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="067da-353">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="067da-353">Scaffold the movie model</span></span>

<span data-ttu-id="067da-354">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="067da-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="067da-355">Diğer bir deyişle, scafkatlama aracı Create Film modeli için sayfa, okuma, güncelleştirme ve Delete (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-357">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="067da-358">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="067da-359">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="067da-360">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="067da-362">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="067da-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="067da-364">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="067da-365">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="067da-366">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı Razor pagesmovie 'den değiştirin. **Modeller**. Razor Pagesmovie 'e PagesMovieContext Razor . **Veri**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="067da-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie. **Models**.RazorPagesMovieContext to RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="067da-367">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="067da-368">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="067da-369">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-369">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="067da-371">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="067da-373">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="067da-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="067da-374">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="067da-375">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="067da-376">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="067da-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="067da-377">Seçenek</span><span class="sxs-lookup"><span data-stu-id="067da-377">Option</span></span>               | <span data-ttu-id="067da-378">Açıklama</span><span class="sxs-lookup"><span data-stu-id="067da-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="067da-379">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="067da-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="067da-380">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="067da-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="067da-381">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="067da-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="067da-382">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="067da-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="067da-383">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler Create</span><span class="sxs-lookup"><span data-stu-id="067da-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="067da-384">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="067da-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="067da-385">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="067da-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="067da-386">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="067da-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="067da-387">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="067da-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="067da-388">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="067da-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="067da-389">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="067da-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-390">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067da-391">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="067da-392">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="067da-393">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="067da-394">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.</span><span class="sxs-lookup"><span data-stu-id="067da-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="067da-396">**Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="067da-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="067da-398">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="067da-399">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( Razor pagesmovie. modeller)**.</span><span class="sxs-lookup"><span data-stu-id="067da-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="067da-400">**Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, Razor pagesfilmi. **Veri**. Razor PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="067da-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie. **Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="067da-401">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="067da-402">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="067da-403">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-403">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="067da-405">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="067da-406">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-406">Add EF tools</span></span>

<span data-ttu-id="067da-407">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="067da-408">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="067da-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="067da-409">Daha fazla bilgi için bkz. [Entity Framework Core araçları başvurusu-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="067da-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="067da-410">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="067da-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="067da-411">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="067da-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="067da-412">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="067da-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="067da-413">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="067da-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="067da-414">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="067da-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-416">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="067da-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="067da-417">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-417">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="067da-418">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="067da-419">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="067da-419">Updated</span></span>

* <span data-ttu-id="067da-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-420">*Startup.cs*</span></span>

<span data-ttu-id="067da-421">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-422">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067da-423">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="067da-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="067da-424">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-424">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="067da-425">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="067da-426">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="067da-426">Updated</span></span>

* <span data-ttu-id="067da-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-427">*Startup.cs*</span></span>

<span data-ttu-id="067da-428">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="067da-430">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="067da-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="067da-431">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-431">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="067da-432">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="067da-433">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="067da-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-435">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="067da-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="067da-436">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-436">Add an initial migration.</span></span>
* <span data-ttu-id="067da-437">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="067da-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="067da-438">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="067da-440">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="067da-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-441">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="067da-442">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="067da-443">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="067da-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="067da-444">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="067da-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="067da-445">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="067da-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="067da-446">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="067da-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="067da-447">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="067da-448">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="067da-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="067da-449">`InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="067da-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="067da-450">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="067da-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="067da-451">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="067da-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="067da-452">Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _Initial Create . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="067da-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="067da-454">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="067da-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="067da-455">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="067da-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="067da-456">EF Core veritabanı bağlamı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="067da-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="067da-457">Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="067da-458">Bir veritabanı bağlamı bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="067da-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="067da-459">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="067da-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="067da-460">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="067da-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="067da-461">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="067da-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="067da-462">`RazorPagesMovieContext`Koordinat, Create model için, okuma, güncelleştirme ve gibi işlevleri EF Core Delete `Movie` .</span><span class="sxs-lookup"><span data-stu-id="067da-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="067da-463">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="067da-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="067da-464">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="067da-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="067da-465">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="067da-466">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="067da-467">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="067da-468">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="067da-469">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="067da-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-470">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="067da-471">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="067da-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="067da-472">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="067da-472">Test the app</span></span>

* <span data-ttu-id="067da-473">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="067da-474">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="067da-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="067da-475">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="067da-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="067da-476">Bağlantıyı test edin **Create** .</span><span class="sxs-lookup"><span data-stu-id="067da-476">Test the **Create** link.</span></span>

  ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="067da-478">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="067da-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="067da-479">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="067da-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="067da-480">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="067da-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="067da-481">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="067da-481">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="067da-482">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="067da-483">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="067da-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="067da-484">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="067da-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="067da-485">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="067da-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="067da-486">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="067da-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="067da-487">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="067da-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="067da-488">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="067da-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="067da-489">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="067da-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="067da-490">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="067da-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="067da-491">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="067da-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="067da-492">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-494">Yeni klasör **eklemek** > **Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="067da-495">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-495">Name the folder *Models*.</span></span>

<span data-ttu-id="067da-496">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="067da-497">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="067da-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="067da-498">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-498">Name the class **Movie**.</span></span>

<span data-ttu-id="067da-499">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-500">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-501">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-502">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-503">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-503">With this attribute:</span></span>

  * <span data-ttu-id="067da-504">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-505">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="067da-506">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="067da-508">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="067da-509">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="067da-510">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-511">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-512">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-513">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-514">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-514">With this attribute:</span></span>

  * <span data-ttu-id="067da-515">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-516">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="067da-517">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="067da-518">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="067da-519">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="067da-519">Add a database context class</span></span>

<span data-ttu-id="067da-520">RazorPagesmovie projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="067da-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="067da-521">Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="067da-522">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="067da-523">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="067da-524">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="067da-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="067da-525">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="067da-525">Add a database connection string</span></span>

<span data-ttu-id="067da-526">*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="067da-527">Gerekli NuGet paketlerini ekleyin</span><span class="sxs-lookup"><span data-stu-id="067da-527">Add required NuGet packages</span></span>

<span data-ttu-id="067da-528">SQLite ve CodeGeneration eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın. projeye tasarım:</span><span class="sxs-lookup"><span data-stu-id="067da-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="067da-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Paket, yapı iskelesi için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="067da-530">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="067da-530">Register the database context</span></span>

<span data-ttu-id="067da-531">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="067da-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="067da-532">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="067da-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="067da-533">Hataları denetlemek için projeyi bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-534">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="067da-535">**Çözüm aracı penceresinde** , *Razor pagesmovie* projesine denetim yapın ve ardından **Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-535">In **Solution Tool Window** , control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="067da-536">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="067da-537">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="067da-538">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="067da-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="067da-539">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="067da-540">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="067da-541">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="067da-542">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="067da-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="067da-543">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="067da-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="067da-544">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="067da-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="067da-545">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="067da-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="067da-546">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="067da-546">With this attribute:</span></span>

  * <span data-ttu-id="067da-547">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="067da-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="067da-548">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="067da-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="067da-549">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="067da-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="067da-550">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="067da-551">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="067da-551">Scaffold the movie model</span></span>

<span data-ttu-id="067da-552">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="067da-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="067da-553">Diğer bir deyişle, scafkatlama aracı Create Film modeli için sayfa, okuma, güncelleştirme ve Delete (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-555">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="067da-556">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="067da-557">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="067da-558">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="067da-560">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="067da-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="067da-562">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="067da-563">**Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="067da-564">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **Razor pagesmovie. modeller adlı adı kabul edin. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="067da-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="067da-565">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-565">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="067da-567">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="067da-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="067da-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="067da-569">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="067da-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="067da-570">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="067da-571">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="067da-572">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="067da-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="067da-573">Seçenek</span><span class="sxs-lookup"><span data-stu-id="067da-573">Option</span></span>               | <span data-ttu-id="067da-574">Açıklama</span><span class="sxs-lookup"><span data-stu-id="067da-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="067da-575">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="067da-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="067da-576">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="067da-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="067da-577">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="067da-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="067da-578">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="067da-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="067da-579">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler Create</span><span class="sxs-lookup"><span data-stu-id="067da-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="067da-580">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="067da-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="067da-581">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="067da-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="067da-582">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="067da-583">Create*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="067da-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="067da-584">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="067da-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="067da-585">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="067da-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="067da-586">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="067da-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="067da-588">**Yeni yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="067da-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="067da-590">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="067da-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="067da-591">**Model sınıfı** açılan kutusunda **film** ' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="067da-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="067da-592">**Veri bağlamı sınıfı** satırında, **Razor pagesmoviecontext** ' i seçin. Bu, doğru ad alanıyla yeni bir veritabanı bağlamı sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="067da-593">Bu durumda, **Razor pagesmovie. modeller olacaktır. Razor PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="067da-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="067da-594">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-594">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="067da-596">*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="067da-597">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="067da-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="067da-598">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="067da-598">Files created</span></span>

* <span data-ttu-id="067da-599">*Sayfalar/filmler* : Create , Delete , ayrıntılar, Düzenle ve Index .</span><span class="sxs-lookup"><span data-stu-id="067da-599">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="067da-600">*Veri/ Razor PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="067da-601">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="067da-601">File updated</span></span>

* <span data-ttu-id="067da-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="067da-602">*Startup.cs*</span></span>

<span data-ttu-id="067da-603">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="067da-604">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="067da-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="067da-606">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="067da-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="067da-607">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-607">Add an initial migration.</span></span>
* <span data-ttu-id="067da-608">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="067da-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="067da-609">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="067da-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="067da-611">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="067da-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="067da-612">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="067da-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="067da-613">Şema, `DbContext` *Razor PagesMovieContext.cs* dosyasında belirtilen modeli temel alır.</span><span class="sxs-lookup"><span data-stu-id="067da-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="067da-614">`InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="067da-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="067da-615">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="067da-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="067da-616">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="067da-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="067da-617">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _Initial Create . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="067da-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="067da-618">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-619">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="067da-620">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="067da-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="067da-621">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="067da-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="067da-622">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="067da-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="067da-623">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="067da-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="067da-624">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="067da-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="067da-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="067da-626">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="067da-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="067da-627">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="067da-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="067da-628">Hizmetler (EF Core veritabanı bağlamı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="067da-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="067da-629">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="067da-630">Bir veritabanı bağlamı contextB bağlam örneğini alan Oluşturucu kodu, daha sonra öğreticide gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="067da-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="067da-631">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="067da-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="067da-632">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="067da-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="067da-633">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="067da-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="067da-634">`RazorPagesMovieContext`Koordinat, Create model için, okuma, güncelleştirme ve gibi işlevleri EF Core Delete `Movie` .</span><span class="sxs-lookup"><span data-stu-id="067da-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="067da-635">Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="067da-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="067da-636">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="067da-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="067da-637">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="067da-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="067da-638">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="067da-639">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="067da-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="067da-640">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="067da-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="067da-641">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="067da-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="067da-642">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="067da-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="067da-643">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="067da-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="067da-644">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="067da-644">Test the app</span></span>

* <span data-ttu-id="067da-645">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="067da-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="067da-646">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="067da-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="067da-647">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="067da-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="067da-648">Bağlantıyı test edin **Create** .</span><span class="sxs-lookup"><span data-stu-id="067da-648">Test the **Create** link.</span></span>

  ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="067da-650">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="067da-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="067da-651">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="067da-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="067da-652">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="067da-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="067da-653">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="067da-653">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="067da-654">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="067da-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="067da-655">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="067da-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="067da-656">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="067da-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
