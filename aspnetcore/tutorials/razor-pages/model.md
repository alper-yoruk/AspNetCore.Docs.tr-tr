---
title: Bölüm 2, model ekleme
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 2. bölümü :::no-loc(Razor)::: . Bu bölümde, model sınıfları eklenir.'
ms.author: riande
ms.date: 09/30/2020
no-loc:
- ':::no-loc(Index):::'
- ':::no-loc(Create):::'
- ':::no-loc(Delete):::'
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
ms.openlocfilehash: ee1b7d77d8d209a11669ba29c8a951c59368180e
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361074"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="e90e7-104">Bölüm 2, :::no-loc(Razor)::: ASP.NET Core bir sayfalar uygulamasına bir model ekleyin</span><span class="sxs-lookup"><span data-stu-id="e90e7-104">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e90e7-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e90e7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e90e7-106">Bu bölümde, bir veritabanında film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="e90e7-107">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e90e7-108">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="e90e7-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="e90e7-109">Önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="e90e7-110">Model sınıfları, EF Core bağımlılığı olmadığından POCO sınıfları (" **P** Lain- **O** ld **C** LR **o** bjects") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-110">The model classes are known as POCO classes (from " **P** lain- **O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="e90e7-111">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e90e7-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e90e7-112">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e90e7-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e90e7-113">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e90e7-115">**Çözüm Gezgini** , yeni klasör ekle > *:::no-loc(Razor)::: pagesmovie* projesine sağ tıklayın **Add**  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-115">In **Solution Explorer** , right-click the *:::no-loc(Razor):::PagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e90e7-116">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="e90e7-117">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="e90e7-118">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="e90e7-119">Sınıf *filmi* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="e90e7-120">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-121">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-122">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-123">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-124">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="e90e7-128">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="e90e7-129">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e90e7-130">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-131">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-132">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-133">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-134">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-134">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-135">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-136">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e90e7-137">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e90e7-138">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-138">Add a database context class</span></span>

1. <span data-ttu-id="e90e7-139">*:::no-loc(Razor)::: Pagesmovie* projesinde, *Data* adlı bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e90e7-139">In the *:::no-loc(Razor):::PagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="e90e7-140">*Veri* klasörüne aşağıdaki kodla *:::no-loc(Razor)::: PagesMovieContext.cs* adlı bir dosya ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-140">In the *Data* folder, add a file named *:::no-loc(Razor):::PagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

   <span data-ttu-id="e90e7-141">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e90e7-142">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e90e7-143">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="e90e7-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e90e7-144">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="e90e7-144">Add a database connection string</span></span>

<span data-ttu-id="e90e7-145">*:::no-loc(appsettings.json):::* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-145">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e90e7-146">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="e90e7-146">Register the database context</span></span>

1. <span data-ttu-id="e90e7-147">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-147">Add the following `using` statements at the top of *Startup.cs* :</span></span>

   ```csharp
   using :::no-loc(Razor):::PagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="e90e7-148">Veritabanı bağlamını, içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-149">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e90e7-150">**Çözüm aracı penceresinde** , *:::no-loc(Razor)::: pagesmovie* projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-150">In the **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="e90e7-151">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="e90e7-152">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="e90e7-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="e90e7-153">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="e90e7-154">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="e90e7-155">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="e90e7-156">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-157">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-158">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-159">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-160">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-160">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-161">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-162">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e90e7-163">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e90e7-164">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e90e7-165">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="e90e7-165">Scaffold the movie model</span></span>

<span data-ttu-id="e90e7-166">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e90e7-167">Diğer bir deyişle, scafkatlama aracı :::no-loc(Create)::: Film modeli için sayfa, okuma, güncelleştirme ve :::no-loc(Delete)::: (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-167">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e90e7-169">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-169">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e90e7-170">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e90e7-171">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e90e7-172">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/sca.png)

1. <span data-ttu-id="e90e7-174">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-174">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

1. <span data-ttu-id="e90e7-176">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-176">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e90e7-177">**Model sınıfı** açılan kutusunda **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-177">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="e90e7-178">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="e90e7-179">**Veri bağlamı Ekle** iletişim kutusunda, *:::no-loc(Razor)::: pagemovie. Data sınıfını adlandırın. :::no-loc(Razor)::: PagesMovieContext* oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="e90e7-179">In the **Add Data Context** dialog, the class name *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="e90e7-180">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-180">Select **Add**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="e90e7-182">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-182">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="e90e7-184">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizinine bir komut kabuğu açın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-184">Open a command shell to the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="e90e7-185">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-185">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e90e7-186">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-186">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e90e7-187">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="e90e7-188">Seçenek</span><span class="sxs-lookup"><span data-stu-id="e90e7-188">Option</span></span>               | <span data-ttu-id="e90e7-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e90e7-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e90e7-190">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="e90e7-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e90e7-191">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="e90e7-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e90e7-192">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e90e7-193">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="e90e7-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e90e7-194">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler :::no-loc(Create):::</span><span class="sxs-lookup"><span data-stu-id="e90e7-194">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="e90e7-195">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e90e7-196">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e90e7-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e90e7-197">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="e90e7-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e90e7-198">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e90e7-199">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e90e7-200">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-201">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="e90e7-202">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-202">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="e90e7-203">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="e90e7-204">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="e90e7-205">*Pages/Movies* **Add** > **Yeni yapı iskelesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

1. <span data-ttu-id="e90e7-207">**Yeni yapı iskelesi** iletişim kutusunda, İleri **:::no-loc(Razor)::: Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-207">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="e90e7-209">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-209">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="e90e7-210">**Kullanılacak DbContext sınıfında:** Row, *:::no-loc(Razor)::: pagemovie. Data sınıfını adlandırın. :::no-loc(Razor)::: PagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="e90e7-210">In the **DbContext Class to use:** row, name the class *:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext*.</span></span>
   1. <span data-ttu-id="e90e7-211">**Son** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-211">Select **Finish**.</span></span>

   ![Önceki yönergelerden görüntü.](model/_static/5/arpMac.png)

<span data-ttu-id="e90e7-213">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-213">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e90e7-214">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="e90e7-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e90e7-215">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e90e7-216">Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="e90e7-217">`IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e90e7-218">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="e90e7-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-220">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e90e7-221">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-221">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="e90e7-222">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-222">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e90e7-223">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e90e7-223">Updated</span></span>

* <span data-ttu-id="e90e7-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-224">*Startup.cs*</span></span>

<span data-ttu-id="e90e7-225">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e90e7-227">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e90e7-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e90e7-228">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-228">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="e90e7-229">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-230">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e90e7-231">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e90e7-232">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-232">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="e90e7-233">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-233">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e90e7-234">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e90e7-234">Updated</span></span>

* <span data-ttu-id="e90e7-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-235">*Startup.cs*</span></span>

<span data-ttu-id="e90e7-236">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="e90e7-237">:::no-loc(Create)::: EF 'in geçiş özelliğini kullanan ilk veritabanı şeması</span><span class="sxs-lookup"><span data-stu-id="e90e7-237">:::no-loc(Create)::: the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="e90e7-238">Entity Framework Core geçiş özelliği şunları yapmak için bir yol sağlar:</span><span class="sxs-lookup"><span data-stu-id="e90e7-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="e90e7-239">:::no-loc(Create)::: İlk veritabanı şeması.</span><span class="sxs-lookup"><span data-stu-id="e90e7-239">:::no-loc(Create)::: the initial database schema.</span></span>
* <span data-ttu-id="e90e7-240">Veritabanı şemasını uygulamanın veri modeliyle eşitlenmiş halde tutmak için artımlı olarak güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="e90e7-241">Veritabanındaki mevcut veriler korunur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-243">Bu bölümde, için **Paket Yöneticisi konsolu** (PMC) penceresi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e90e7-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="e90e7-244">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-244">Add an initial migration.</span></span>
* <span data-ttu-id="e90e7-245">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="e90e7-246">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC menüsü](model/_static/5/pmc.png)

1. <span data-ttu-id="e90e7-248">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial:::no-loc(Create):::
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-249">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="e90e7-250">Aşağıdaki .NET CLı komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add Initial:::no-loc(Create):::
  dotnet ef database update
  ```

---

<span data-ttu-id="e90e7-251">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e90e7-252">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e90e7-253">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="e90e7-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e90e7-254">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e90e7-255">`migrations`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e90e7-256">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e90e7-257">`Initial:::no-loc(Create):::`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-257">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="e90e7-258">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e90e7-259">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e90e7-260">Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e90e7-262">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="e90e7-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e90e7-263">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e90e7-264">EF Core veritabanı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e90e7-265">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-265">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e90e7-266">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e90e7-267">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e90e7-268">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e90e7-269">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="e90e7-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e90e7-270">`:::no-loc(Razor):::PagesMovieContext`Koordinat, :::no-loc(Create)::: model için, okuma, güncelleştirme ve gibi işlevleri EF Core :::no-loc(Delete)::: `Movie` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-270">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="e90e7-271">Veri bağlamı ( `:::no-loc(Razor):::PagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-271">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e90e7-272">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie50/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="e90e7-273">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="e90e7-274">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e90e7-275">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e90e7-276">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e90e7-277">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *:::no-loc(appsettings.json):::* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-278">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e90e7-279">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="e90e7-280">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="e90e7-280">Test the app</span></span>

1. <span data-ttu-id="e90e7-281">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="e90e7-282">Aşağıdaki hata iletisini alırsanız:</span><span class="sxs-lookup"><span data-stu-id="e90e7-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="e90e7-283">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="e90e7-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="e90e7-284">Bağlantıyı test edin **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-284">Test the **:::no-loc(Create):::** link.</span></span>

   ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="e90e7-286">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e90e7-287">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e90e7-288">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="e90e7-289">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-289">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="e90e7-290">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e90e7-291">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e90e7-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e90e7-292">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi :::no-loc(Razor)::: Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e90e7-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e90e7-293">Bu bölümde, film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="e90e7-294">Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="e90e7-295">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).</span><span class="sxs-lookup"><span data-stu-id="e90e7-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="e90e7-296">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e90e7-297">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e90e7-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e90e7-298">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e90e7-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e90e7-299">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-301">Yeni klasör **eklemek** > **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-301">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e90e7-302">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-302">Name the folder *Models*.</span></span>

<span data-ttu-id="e90e7-303">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="e90e7-304">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="e90e7-305">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-305">Name the class **Movie**.</span></span>

<span data-ttu-id="e90e7-306">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-307">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-308">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-309">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-310">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-310">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-311">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-312">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e90e7-313">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e90e7-315">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e90e7-316">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e90e7-317">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-318">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-319">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-320">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-321">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-321">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-322">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-323">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e90e7-324">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e90e7-325">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e90e7-326">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-326">Add a database context class</span></span>

* <span data-ttu-id="e90e7-327">*:::no-loc(Razor)::: Pagesmovie* projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e90e7-327">In the *:::no-loc(Razor):::PagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="e90e7-328">Aşağıdaki `:::no-loc(Razor):::PagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-328">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="e90e7-329">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e90e7-330">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e90e7-331">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="e90e7-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e90e7-332">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="e90e7-332">Add a database connection string</span></span>

<span data-ttu-id="e90e7-333">*:::no-loc(appsettings.json):::* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-333">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e90e7-334">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="e90e7-334">Register the database context</span></span>

<span data-ttu-id="e90e7-335">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-335">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e90e7-336">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-337">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e90e7-338">**Çözüm aracı penceresinde** , **:::no-loc(Razor)::: pagesmovie** projesine denetim yapın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-338">In the **Solution Tool Window** , control-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e90e7-339">*Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e90e7-340">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="e90e7-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e90e7-341">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e90e7-342">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e90e7-343">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e90e7-344">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-345">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-346">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-347">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-348">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-348">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-349">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-350">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="e90e7-351">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="e90e7-352">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e90e7-353">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="e90e7-353">Scaffold the movie model</span></span>

<span data-ttu-id="e90e7-354">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e90e7-355">Diğer bir deyişle, scafkatlama aracı :::no-loc(Create)::: Film modeli için sayfa, okuma, güncelleştirme ve :::no-loc(Delete)::: (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-355">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-357">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-357">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e90e7-358">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e90e7-359">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="e90e7-360">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="e90e7-362">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-362">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="e90e7-364">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-364">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e90e7-365">**Model sınıfı** açılan kutusunda **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-365">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="e90e7-366">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı :::no-loc(Razor)::: pagesmovie 'den değiştirin. **Modeller**. :::no-loc(Razor)::: Pagesmovie 'e PagesMovieContext :::no-loc(Razor)::: . **Veri**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e90e7-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="e90e7-367">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e90e7-368">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e90e7-369">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-369">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

<span data-ttu-id="e90e7-371">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-371">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="e90e7-373">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-373">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="e90e7-374">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-374">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e90e7-375">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-375">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e90e7-376">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e90e7-377">Seçenek</span><span class="sxs-lookup"><span data-stu-id="e90e7-377">Option</span></span>               | <span data-ttu-id="e90e7-378">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e90e7-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e90e7-379">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="e90e7-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e90e7-380">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="e90e7-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e90e7-381">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e90e7-382">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="e90e7-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e90e7-383">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler :::no-loc(Create):::</span><span class="sxs-lookup"><span data-stu-id="e90e7-383">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="e90e7-384">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e90e7-385">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e90e7-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e90e7-386">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="e90e7-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e90e7-387">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e90e7-388">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="e90e7-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e90e7-389">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-390">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e90e7-391">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-391">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e90e7-392">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e90e7-393">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="e90e7-394">Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="e90e7-396">**Yeni yapı iskelesi** iletişim kutusunda, İleri **:::no-loc(Razor)::: Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-396">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e90e7-398">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-398">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e90e7-399">**Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( :::no-loc(Razor)::: pagesmovie. modeller)**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-399">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="e90e7-400">**Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, :::no-loc(Razor)::: pagesfilmi. **Veri**. :::no-loc(Razor)::: PagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e90e7-400">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="e90e7-401">[Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e90e7-402">Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e90e7-403">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-403">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="e90e7-405">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-405">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="e90e7-406">EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-406">Add EF tools</span></span>

<span data-ttu-id="e90e7-407">Aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="e90e7-408">Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.</span><span class="sxs-lookup"><span data-stu-id="e90e7-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="e90e7-409">Daha fazla bilgi için bkz. [Entity Framework Core araçları başvurusu-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="e90e7-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="e90e7-410">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="e90e7-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="e90e7-411">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="e90e7-412">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="e90e7-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="e90e7-413">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="e90e7-414">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="e90e7-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-416">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e90e7-417">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-417">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="e90e7-418">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-418">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e90e7-419">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e90e7-419">Updated</span></span>

* <span data-ttu-id="e90e7-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-420">*Startup.cs*</span></span>

<span data-ttu-id="e90e7-421">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-422">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e90e7-423">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e90e7-424">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-424">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="e90e7-425">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-425">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e90e7-426">Güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e90e7-426">Updated</span></span>

* <span data-ttu-id="e90e7-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-427">*Startup.cs*</span></span>

<span data-ttu-id="e90e7-428">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e90e7-430">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e90e7-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e90e7-431">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-431">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>

<span data-ttu-id="e90e7-432">Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e90e7-433">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="e90e7-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-435">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e90e7-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e90e7-436">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-436">Add an initial migration.</span></span>
* <span data-ttu-id="e90e7-437">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="e90e7-438">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e90e7-440">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial:::no-loc(Create):::
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-441">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e90e7-442">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="e90e7-443">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e90e7-444">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e90e7-445">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="e90e7-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e90e7-446">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="e90e7-447">Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e90e7-448">Şema, içinde belirtilen modeli temel alır `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e90e7-449">`Initial:::no-loc(Create):::`Bağımsız değişken, geçişleri adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-449">The `Initial:::no-loc(Create):::` argument is used to name the migrations.</span></span> <span data-ttu-id="e90e7-450">Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e90e7-451">`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e90e7-452">Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e90e7-454">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="e90e7-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e90e7-455">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e90e7-456">EF Core veritabanı bağlamı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e90e7-457">Sayfalar gibi bu hizmetleri gerektiren bileşenler :::no-loc(Razor)::: , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-457">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="e90e7-458">Bir veritabanı bağlamı bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e90e7-459">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e90e7-460">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e90e7-461">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="e90e7-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e90e7-462">`:::no-loc(Razor):::PagesMovieContext`Koordinat, :::no-loc(Create)::: model için, okuma, güncelleştirme ve gibi işlevleri EF Core :::no-loc(Delete)::: `Movie` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-462">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="e90e7-463">Veri bağlamı ( `:::no-loc(Razor):::PagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-463">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e90e7-464">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="e90e7-465">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e90e7-466">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e90e7-467">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e90e7-468">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e90e7-469">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *:::no-loc(appsettings.json):::* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-470">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e90e7-471">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e90e7-472">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="e90e7-472">Test the app</span></span>

* <span data-ttu-id="e90e7-473">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e90e7-474">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="e90e7-474">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e90e7-475">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="e90e7-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e90e7-476">Bağlantıyı test edin **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-476">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="e90e7-478">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e90e7-479">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e90e7-480">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e90e7-481">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-481">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="e90e7-482">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e90e7-483">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e90e7-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e90e7-484">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi :::no-loc(Razor)::: Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e90e7-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e90e7-485">Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e90e7-486">Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e90e7-487">Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e90e7-488">EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e90e7-489">Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e90e7-490">Veritabanında depolanan verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="e90e7-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="e90e7-491">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e90e7-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="e90e7-492">Veri modeli ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-494">Yeni klasör **eklemek** > **:::no-loc(Razor)::: pagesmovie** projesine sağ tıklayın  >  **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-494">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e90e7-495">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-495">Name the folder *Models*.</span></span>

<span data-ttu-id="e90e7-496">*Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="e90e7-497">Sınıf **Ekle** ' yi seçin  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="e90e7-498">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-498">Name the class **Movie**.</span></span>

<span data-ttu-id="e90e7-499">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-500">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-501">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-502">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-503">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-503">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-504">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-505">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e90e7-506">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e90e7-508">*Modeller* adlı bir klasör ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e90e7-509">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="e90e7-510">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-511">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-512">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-513">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-514">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-514">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-515">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-516">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e90e7-517">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="e90e7-518">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="e90e7-519">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="e90e7-519">Add a database context class</span></span>

<span data-ttu-id="e90e7-520">:::no-loc(Razor):::Pagesmovie projesinde, *Data* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e90e7-520">In the :::no-loc(Razor):::PagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="e90e7-521">Aşağıdaki `:::no-loc(Razor):::PagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-521">Add the following `:::no-loc(Razor):::PagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="e90e7-522">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="e90e7-523">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="e90e7-524">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="e90e7-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="e90e7-525">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="e90e7-525">Add a database connection string</span></span>

<span data-ttu-id="e90e7-526">*:::no-loc(appsettings.json):::* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-526">Add a connection string to the *:::no-loc(appsettings.json):::* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="e90e7-527">Gerekli NuGet paketlerini ekleyin</span><span class="sxs-lookup"><span data-stu-id="e90e7-527">Add required NuGet packages</span></span>

<span data-ttu-id="e90e7-528">SQLite ve CodeGeneration eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın. projeye tasarım:</span><span class="sxs-lookup"><span data-stu-id="e90e7-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="e90e7-529">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Paket, yapı iskelesi için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="e90e7-530">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="e90e7-530">Register the database context</span></span>

<span data-ttu-id="e90e7-531">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-531">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using :::no-loc(Razor):::PagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="e90e7-532">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="e90e7-533">Hataları denetlemek için projeyi bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-534">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e90e7-535">**Çözüm aracı penceresinde** , *:::no-loc(Razor)::: pagesmovie* projesine denetim yapın ve ardından **Add**  >  **Yeni klasör** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-535">In **Solution Tool Window** , control-click the *:::no-loc(Razor):::PagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e90e7-536">Klasör *modellerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="e90e7-537">*Modeller* klasörünü kontrol edin ve ardından **Add** > **yeni dosya** Ekle ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e90e7-538">**Yeni dosya** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="e90e7-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e90e7-539">Sol bölmedeki **genel** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e90e7-540">Orta bölmede **boş sınıf** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e90e7-541">Sınıfı **filmi** adlandırın ve **Yeni** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="e90e7-542">Sınıfına aşağıdaki özellikleri ekleyin `Movie` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="e90e7-543">`Movie`Sınıfı şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="e90e7-544">`ID`Alan, birincil anahtar için veritabanı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="e90e7-545">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="e90e7-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="e90e7-546">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="e90e7-546">With this attribute:</span></span>

  * <span data-ttu-id="e90e7-547">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="e90e7-548">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="e90e7-549">[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="e90e7-550">Derleme hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e90e7-551">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="e90e7-551">Scaffold the movie model</span></span>

<span data-ttu-id="e90e7-552">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e90e7-553">Diğer bir deyişle, scafkatlama aracı :::no-loc(Create)::: Film modeli için sayfa, okuma, güncelleştirme ve :::no-loc(Delete)::: (CRUD) işlemleri üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-553">That is, the scaffolding tool produces pages for :::no-loc(Create):::, Read, Update, and :::no-loc(Delete)::: (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-555">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-555">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e90e7-556">Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e90e7-557">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="e90e7-558">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/sca.png)

<span data-ttu-id="e90e7-560">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-560">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

<span data-ttu-id="e90e7-562">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-562">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e90e7-563">**Model sınıfı** açılan kutusunda **Film ( :::no-loc(Razor)::: pagesmovie. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-563">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="e90e7-564">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **:::no-loc(Razor)::: pagesmovie. modeller adlı adı kabul edin. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="e90e7-565">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-565">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arp.png)

<span data-ttu-id="e90e7-567">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-567">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e90e7-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e90e7-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="e90e7-569">*Program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-569">Open a command window in the project directory, which contains the *Program.cs* , *Startup.cs* , and *.csproj* files.</span></span>

* <span data-ttu-id="e90e7-570">**Windows için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-570">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e90e7-571">**MacOS ve Linux için** : aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-571">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="e90e7-572">Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="e90e7-573">Seçenek</span><span class="sxs-lookup"><span data-stu-id="e90e7-573">Option</span></span>               | <span data-ttu-id="e90e7-574">Açıklama</span><span class="sxs-lookup"><span data-stu-id="e90e7-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="e90e7-575">Modelin adı.</span><span class="sxs-lookup"><span data-stu-id="e90e7-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="e90e7-576">`DbContext`Kullanılacak sınıf.</span><span class="sxs-lookup"><span data-stu-id="e90e7-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="e90e7-577">Varsayılan düzeni kullanın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="e90e7-578">Görünümleri oluşturmak için göreli çıkış klasörü yolu.</span><span class="sxs-lookup"><span data-stu-id="e90e7-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="e90e7-579">`_ValidationScriptsPartial`Düzenleme ve sayfalara ekler :::no-loc(Create):::</span><span class="sxs-lookup"><span data-stu-id="e90e7-579">Adds `_ValidationScriptsPartial` to Edit and :::no-loc(Create)::: pages</span></span> |

<span data-ttu-id="e90e7-580">`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="e90e7-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="e90e7-581">Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e90e7-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-582">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e90e7-583">:::no-loc(Create):::*Sayfalar/filmler* klasörü:</span><span class="sxs-lookup"><span data-stu-id="e90e7-583">:::no-loc(Create)::: a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e90e7-584">Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > **New Folder**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e90e7-585">Klasör *filmlerini* adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="e90e7-586">*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

<span data-ttu-id="e90e7-588">**Yeni yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-588">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e90e7-590">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e90e7-590">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e90e7-591">**Model sınıfı** açılan kutusunda **film** ' ı seçin veya yazın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e90e7-592">**Veri bağlamı sınıfı** satırında, **:::no-loc(Razor)::: pagesmoviecontext** ' i seçin. Bu, doğru ad alanıyla yeni bir veritabanı bağlamı sınıfı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-592">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="e90e7-593">Bu durumda, **:::no-loc(Razor)::: pagesmovie. modeller olacaktır. :::no-loc(Razor)::: PagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e90e7-593">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="e90e7-594">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-594">Select **Add**.</span></span>

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

<span data-ttu-id="e90e7-596">*:::no-loc(appsettings.json):::* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-596">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e90e7-597">Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="e90e7-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e90e7-598">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="e90e7-598">Files created</span></span>

* <span data-ttu-id="e90e7-599">*Sayfalar/filmler* : :::no-loc(Create)::: , :::no-loc(Delete)::: , ayrıntılar, Düzenle ve :::no-loc(Index)::: .</span><span class="sxs-lookup"><span data-stu-id="e90e7-599">*Pages/Movies* : :::no-loc(Create):::, :::no-loc(Delete):::, Details, Edit, and :::no-loc(Index):::.</span></span>
* <span data-ttu-id="e90e7-600">*Veri/ :::no-loc(Razor)::: PagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-600">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e90e7-601">Dosya güncelleştirildi</span><span class="sxs-lookup"><span data-stu-id="e90e7-601">File updated</span></span>

* <span data-ttu-id="e90e7-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e90e7-602">*Startup.cs*</span></span>

<span data-ttu-id="e90e7-603">Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e90e7-604">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="e90e7-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e90e7-606">Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="e90e7-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e90e7-607">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-607">Add an initial migration.</span></span>
* <span data-ttu-id="e90e7-608">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="e90e7-609">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e90e7-611">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="e90e7-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e90e7-612">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e90e7-613">Şema, `DbContext` *:::no-loc(Razor)::: PagesMovieContext.cs* dosyasında belirtilen modeli temel alır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-613">The schema is based on the model specified in the `DbContext`, in the *:::no-loc(Razor):::PagesMovieContext.cs* file.</span></span> <span data-ttu-id="e90e7-614">`Initial:::no-loc(Create):::`Bağımsız değişkeni, geçişi adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-614">The `Initial:::no-loc(Create):::` argument is used to name the migration.</span></span> <span data-ttu-id="e90e7-615">Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e90e7-616">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e90e7-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e90e7-617">`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _Initial :::no-loc(Create)::: . cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_Initial:::no-loc(Create):::.cs* file.</span></span> <span data-ttu-id="e90e7-618">`Up`Yöntemi veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-619">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="e90e7-620">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e90e7-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add Initial:::no-loc(Create):::
dotnet ef database update
```

---

<span data-ttu-id="e90e7-621">Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e90e7-622">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e90e7-623">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "</span><span class="sxs-lookup"><span data-stu-id="e90e7-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e90e7-624">Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e90e7-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e90e7-626">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="e90e7-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e90e7-627">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e90e7-628">Hizmetler (EF Core veritabanı bağlamı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e90e7-629">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-629">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e90e7-630">Bir veritabanı bağlamı contextB bağlam örneğini alan Oluşturucu kodu, daha sonra öğreticide gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e90e7-631">Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="e90e7-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e90e7-632">Yöntemini inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e90e7-633">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="e90e7-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e90e7-634">`:::no-loc(Razor):::PagesMovieContext`Koordinat, :::no-loc(Create)::: model için, okuma, güncelleştirme ve gibi işlevleri EF Core :::no-loc(Delete)::: `Movie` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-634">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality, such as :::no-loc(Create):::, Read, Update, and :::no-loc(Delete):::, for the `Movie` model.</span></span> <span data-ttu-id="e90e7-635">Veri bağlamı ( `:::no-loc(Razor):::PagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-635">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="e90e7-636">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="e90e7-637">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e90e7-638">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e90e7-639">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e90e7-640">Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="e90e7-641">Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *:::no-loc(appsettings.json):::* dosyadan okur.</span><span class="sxs-lookup"><span data-stu-id="e90e7-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e90e7-642">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e90e7-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e90e7-643">Yöntemini inceleyin `Up` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e90e7-644">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="e90e7-644">Test the app</span></span>

* <span data-ttu-id="e90e7-645">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e90e7-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e90e7-646">Şu hatayı alırsanız:</span><span class="sxs-lookup"><span data-stu-id="e90e7-646">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e90e7-647">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="e90e7-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e90e7-648">Bağlantıyı test edin **:::no-loc(Create):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-648">Test the **:::no-loc(Create):::** link.</span></span>

  ![::: No-Loc (Oluştur)::: sayfa](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e90e7-650">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="e90e7-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e90e7-651">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e90e7-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e90e7-652">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="e90e7-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e90e7-653">**Düzenleme** , **Ayrıntılar** ve bağlantıları test edin **:::no-loc(Delete):::** .</span><span class="sxs-lookup"><span data-stu-id="e90e7-653">Test the **Edit** , **Details** , and **:::no-loc(Delete):::** links.</span></span>

<span data-ttu-id="e90e7-654">Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e90e7-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e90e7-655">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e90e7-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e90e7-656">[Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi :::no-loc(Razor)::: Sayfalar](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e90e7-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
