---
title: Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku
author: rick-anderson
description: RazorSayfaların 6. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e67738015f64ca7077c2f87a8f7eabe722aac9d8
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652622"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="c0fdc-103">Bölüm 6, Razor ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku</span><span class="sxs-lookup"><span data-stu-id="c0fdc-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="c0fdc-104">, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0fdc-105">Bu öğreticide, ilgili verilerin nasıl okunacağı ve görüntüleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="c0fdc-106">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="c0fdc-107">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="c0fdc-110">Eager, açık ve yavaş yükleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="c0fdc-111">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="c0fdc-112">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="c0fdc-113">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="c0fdc-114">Bir varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="c0fdc-115">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="c0fdc-116">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="c0fdc-117">Birden çok sorgu verme, çok büyük paketlerini tek sorgusundan daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="c0fdc-118">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="c0fdc-120">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="c0fdc-121">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="c0fdc-121">One query for the main query</span></span> 
  * <span data-ttu-id="c0fdc-122">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="c0fdc-123">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="c0fdc-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="c0fdc-124">"Düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="c0fdc-125">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="c0fdc-127">Note: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklar için gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="c0fdc-128">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="c0fdc-129">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="c0fdc-130">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="c0fdc-131">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="c0fdc-132">Ayrı sorgularla açık yükleme, veritabanına birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="c0fdc-133">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="c0fdc-134">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="c0fdc-135">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-135">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="c0fdc-137">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="c0fdc-138">[Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="c0fdc-139">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="c0fdc-140">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="c0fdc-141">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu veritabanına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="c0fdc-142">Kurs sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0fdc-142">Create Course pages</span></span>

<span data-ttu-id="c0fdc-143">`Course`Varlık, ilgili varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<span data-ttu-id="c0fdc-145">Bir kurs için atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="c0fdc-146">İlgili `Department` varlığı `Course.Department` gezinti özelliğine yükleyin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="c0fdc-147">`Department`Varlığın özelliğinden adı alın `Name` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="c0fdc-148">Yapı iskelesi kurs sayfaları</span><span class="sxs-lookup"><span data-stu-id="c0fdc-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0fdc-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0fdc-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0fdc-150">Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="c0fdc-151">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="c0fdc-152">`Course`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="c0fdc-153">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0fdc-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0fdc-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c0fdc-155">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="c0fdc-156">Kurs sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="c0fdc-157">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="c0fdc-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="c0fdc-158">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="c0fdc-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="c0fdc-159">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="c0fdc-160">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="c0fdc-161">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="c0fdc-162">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="c0fdc-163">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="c0fdc-164">Departmanın adını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-164">Display the department name</span></span>

<span data-ttu-id="c0fdc-165">Pages/kurslar/Index. cshtml. cs dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="c0fdc-166">Önceki kod, `Course` özelliği olarak değiştirir `Courses` ve ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="c0fdc-167">`AsNoTracking`Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="c0fdc-168">Varlıkların geçerli bağlamda güncelleştirilmediği için izlenmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="c0fdc-169">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="c0fdc-170">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="c0fdc-171">`Course`Özellik adı olarak değiştirildi `Courses` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="c0fdc-172">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="c0fdc-173">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="c0fdc-174">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="c0fdc-175">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="c0fdc-176">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="c0fdc-177">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="c0fdc-179">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-179">Loading related data with Select</span></span>

<span data-ttu-id="c0fdc-180">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="c0fdc-181">`Select`Yöntemi, yalnızca gerekli ilgili verileri yükleyen bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="c0fdc-182">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="c0fdc-183">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="c0fdc-184">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="c0fdc-185">Yukarıdaki kod hiçbir varlık türü döndürmez, bu nedenle hiçbir izleme yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="c0fdc-186">EF izleme hakkında daha fazla bilgi için bkz. [izleme ve Izleme sorguları karşılaştırması](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="c0fdc-187">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="c0fdc-188">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="c0fdc-189">Eğitmen sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0fdc-189">Create Instructor pages</span></span>

<span data-ttu-id="c0fdc-190">Bu bölüm, eğitmen sayfalarını derler ve ilgili Kurslar ve kayıtları eğitmenler dizin sayfasına ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="c0fdc-191">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="c0fdc-192">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="c0fdc-193">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="c0fdc-194">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="c0fdc-195">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="c0fdc-196">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="c0fdc-197">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="c0fdc-198">Kullanıcı bir eğitmen seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="c0fdc-199">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="c0fdc-200">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="c0fdc-201">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="c0fdc-202">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="c0fdc-203">Kullanıcı bir kurs seçtiğinde, varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="c0fdc-204">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="c0fdc-205">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="c0fdc-206">Görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0fdc-206">Create a view model</span></span>

<span data-ttu-id="c0fdc-207">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="c0fdc-208">Üç tabloyu temsil eden üç özellik içeren bir görünüm modeli gerekir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="c0fdc-209">Aşağıdaki kodla *SchoolViewModels/ıncpctorındexdata. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="c0fdc-210">Yapı iskelesi eğitmeni sayfaları</span><span class="sxs-lookup"><span data-stu-id="c0fdc-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0fdc-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0fdc-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c0fdc-212">Öğrenci sayfalarını aşağıdaki özel durumlarla birlikte [Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) içindeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="c0fdc-213">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="c0fdc-214">`Instructor`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="c0fdc-215">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0fdc-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0fdc-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c0fdc-217">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="c0fdc-218">Eğitmen sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="c0fdc-219">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="c0fdc-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="c0fdc-220">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="c0fdc-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="c0fdc-221">Yapı iskelesi sayfasının güncelleştirmeden önce nasıl göründüğünü görmek için, uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="c0fdc-222">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="c0fdc-223">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="c0fdc-224">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="c0fdc-225">Kod, aşağıdaki gezinti özellikleri için Eager yüklemeyi belirtir:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="c0fdc-226">Ve `Include` `ThenInclude` için ve yöntemlerinin yinelendiğine dikkat `CourseAssignments` edin `Course` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="c0fdc-227">Bu yineleme, varlığın iki gezinti özelliği için Eager yüklemesi belirtmek için gereklidir `Course` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="c0fdc-228">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="c0fdc-229">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="c0fdc-230">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="c0fdc-231">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="c0fdc-232">Ancak bu durumda filtre tek bir varlık seçer.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="c0fdc-233">Bu nedenle, `Single` yöntemi koleksiyonu tek bir varlığa dönüştürmek için çağırılır `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="c0fdc-234">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="c0fdc-235">`CourseAssignments`ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c0fdc-237">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="c0fdc-238">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="c0fdc-239">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="c0fdc-240">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="c0fdc-241">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="c0fdc-241">Update the instructors Index page</span></span>

<span data-ttu-id="c0fdc-242">*Pages/eğitmenler/Index. cshtml* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="c0fdc-243">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="c0fdc-244">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="c0fdc-245">`"{id:int?}"`bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="c0fdc-246">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="c0fdc-247">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="c0fdc-248">Sayfa yönergesi olduğunda `@page "{id:int?}"` URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="c0fdc-249">Yalnızca null değilse görüntülenen bir **Office** sütunu ekler `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="c0fdc-250">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="c0fdc-251">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="c0fdc-252">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="c0fdc-253">`class="success"` `tr` Seçilen eğitmenin ve kursun öğesine dinamik olarak ekleyen kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="c0fdc-254">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="c0fdc-255">**Select**etiketli yeni bir köprü ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="c0fdc-256">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="c0fdc-257">Seçili eğitmen için bir kurs tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="c0fdc-258">Seçili kurs için bir öğrenci kayıtları tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="c0fdc-259">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="c0fdc-260">`OfficeAssignment`Null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="c0fdc-261">Bir eğitmenin **Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="c0fdc-262">Bu eğitmenin atandığı satır stili değişiklikleri ve kurslar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="c0fdc-263">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="c0fdc-265">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="c0fdc-265">Using Single</span></span>

<span data-ttu-id="c0fdc-266">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="c0fdc-267">`Single`Bir Where koşulunun kullanımı, kişisel tercihden bağımsız olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="c0fdc-268">Yöntemi kullanılarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="c0fdc-269">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-269">Explicit loading</span></span>

<span data-ttu-id="c0fdc-270">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="c0fdc-271">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="c0fdc-272">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="c0fdc-273">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="c0fdc-274">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="c0fdc-275">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="c0fdc-276">Bir kurs seçilirse, açık yükleme kodu alır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="c0fdc-277">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="c0fdc-278">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="c0fdc-279">Yukarıdaki kodun yorumdığına dikkat edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="c0fdc-280">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="c0fdc-281">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-281">Test the app.</span></span> <span data-ttu-id="c0fdc-282">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c0fdc-283">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="c0fdc-283">Next steps</span></span>

<span data-ttu-id="c0fdc-284">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="c0fdc-285">[Önceki öğretici](xref:data/ef-rp/complex-data-model) 
> [Sonraki öğretici](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c0fdc-286">Bu öğreticide ilgili veriler okundu ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="c0fdc-287">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="c0fdc-288">Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="c0fdc-289">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="c0fdc-290">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="c0fdc-293">İlgili verilerin Eager, açık ve geç yüklemesi</span><span class="sxs-lookup"><span data-stu-id="c0fdc-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="c0fdc-294">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="c0fdc-295">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="c0fdc-296">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="c0fdc-297">Varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="c0fdc-298">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="c0fdc-299">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="c0fdc-300">Birden çok sorgu verilmesi, EF6 içindeki bazı sorgular için tek bir sorgunun bulunduğu durumda daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="c0fdc-301">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="c0fdc-303">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="c0fdc-304">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="c0fdc-304">One query for the main query</span></span> 
  * <span data-ttu-id="c0fdc-305">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="c0fdc-306">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="c0fdc-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="c0fdc-307">"düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="c0fdc-308">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="c0fdc-310">Note: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklar için gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="c0fdc-311">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="c0fdc-312">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="c0fdc-313">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="c0fdc-314">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="c0fdc-315">Ayrı sorgularla açık yükleme, VERITABANıNA birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="c0fdc-316">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="c0fdc-317">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="c0fdc-318">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-318">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="c0fdc-320">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="c0fdc-321">[Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="c0fdc-322">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="c0fdc-323">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="c0fdc-324">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu VERITABANıNA gönderilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="c0fdc-325">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="c0fdc-326">Bölüm adını görüntüleyen bir kurs sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="c0fdc-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="c0fdc-327">Kurs varlığı, varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="c0fdc-328">`Department`Varlık, kursun atandığı departmanı içerir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="c0fdc-329">Bir kurs listesinde atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="c0fdc-330">`Name`Varlıktaki özelliği alın `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="c0fdc-331">`Department`Varlık, `Course.Department` Gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="c0fdc-333">Kurs modelini temklesi</span><span class="sxs-lookup"><span data-stu-id="c0fdc-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0fdc-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0fdc-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="c0fdc-335">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Course` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0fdc-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0fdc-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="c0fdc-337">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="c0fdc-338">Yukarıdaki komut, modeli bir daha yasaklıyor `Course` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="c0fdc-339">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="c0fdc-340">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="c0fdc-341">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="c0fdc-342">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="c0fdc-343">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="c0fdc-344">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="c0fdc-345">`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="c0fdc-346">Yukarıdaki kod ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="c0fdc-347">`AsNoTracking`Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="c0fdc-348">Geçerli bağlamda güncelleştirilmemiş oldukları için varlıklar izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="c0fdc-349">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="c0fdc-350">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="c0fdc-351">Başlık dizinden kurslar olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="c0fdc-352">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="c0fdc-353">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="c0fdc-354">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="c0fdc-355">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="c0fdc-356">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="c0fdc-357">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="c0fdc-359">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-359">Loading related data with Select</span></span>

<span data-ttu-id="c0fdc-360">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="c0fdc-361">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="c0fdc-362">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="c0fdc-363">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="c0fdc-364">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="c0fdc-365">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="c0fdc-366">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="c0fdc-367">Kurslar ve kayıtları gösteren bir eğitmenler sayfası oluşturun</span><span class="sxs-lookup"><span data-stu-id="c0fdc-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="c0fdc-368">Bu bölümde, Eğitmenler sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="c0fdc-369">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="c0fdc-370">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="c0fdc-371">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="c0fdc-372">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="c0fdc-373">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="c0fdc-374">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="c0fdc-375">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="c0fdc-376">Kullanıcı bir eğitmen (önceki görüntüde Haruı) seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="c0fdc-377">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="c0fdc-378">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="c0fdc-379">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="c0fdc-380">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="c0fdc-381">Kullanıcı bir kurs seçtiğinde (önceki görüntüde Chemistry), varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="c0fdc-382">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="c0fdc-383">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="c0fdc-384">Eğitmen dizini görünümü için bir görünüm modeli oluşturun</span><span class="sxs-lookup"><span data-stu-id="c0fdc-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="c0fdc-385">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="c0fdc-386">Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="c0fdc-387">*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="c0fdc-388">Eğitmen modelini scafkatlama</span><span class="sxs-lookup"><span data-stu-id="c0fdc-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c0fdc-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0fdc-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="c0fdc-390">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Instructor` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0fdc-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0fdc-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="c0fdc-392">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="c0fdc-393">Yukarıdaki komut, modeli bir daha yasaklıyor `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="c0fdc-394">Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="c0fdc-395">*Pages/eğitmenler/Index. cshtml. cs* öğesini şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="c0fdc-396">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="c0fdc-397">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="c0fdc-398">Sorgunun iki içerme vardır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-398">The query has two includes:</span></span>

* <span data-ttu-id="c0fdc-399">`OfficeAssignment`: [Eğitmenler görünümünde](#IP)görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="c0fdc-400">`CourseAssignments`: Kurslar taöğretme.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="c0fdc-401">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="c0fdc-401">Update the instructors Index page</span></span>

<span data-ttu-id="c0fdc-402">*Sayfaları/eğitmenler/Index. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="c0fdc-403">Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="c0fdc-404">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="c0fdc-405">`"{id:int?}"`bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="c0fdc-406">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="c0fdc-407">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="c0fdc-408">Sayfa yönergesi olduğunda `@page "{id:int?}"` , ÖNCEKI URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="c0fdc-409">Sayfa başlığı **eğitmenler**' dir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="c0fdc-410">Yalnızca null olmaması halinde görüntülenen bir **Office** sütunu eklendi `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="c0fdc-411">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="c0fdc-412">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="c0fdc-413">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="c0fdc-414">`class="success"`Seçilen eğitmenin öğesine dinamik olarak ekleyen kod eklendi `tr` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="c0fdc-415">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="c0fdc-416">**Select**etiketli yeni bir köprü eklendi.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="c0fdc-417">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="c0fdc-418">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="c0fdc-419">Eğer OfficeAssignment ' null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="c0fdc-420">**Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-420">Click on the **Select** link.</span></span> <span data-ttu-id="c0fdc-421">Satır stili değişir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="c0fdc-422">Seçili eğitmen 'e göre kurslar ekleyin</span><span class="sxs-lookup"><span data-stu-id="c0fdc-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="c0fdc-423">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* dosyasındaki yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="c0fdc-424">Ekleyemiyorum`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="c0fdc-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="c0fdc-425">Güncelleştirilmiş sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="c0fdc-426">Önceki sorgu `Department` varlıkları ekler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="c0fdc-427">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="c0fdc-428">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="c0fdc-429">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="c0fdc-430">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="c0fdc-431">Önceki `Where` yöntemde yalnızca tek bir `Instructor` varlık döndürülür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="c0fdc-432">`Single`Yöntemi, koleksiyonu tek bir `Instructor` varlığa dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="c0fdc-433">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="c0fdc-434">`CourseAssignments`ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="c0fdc-436">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="c0fdc-437">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="c0fdc-438">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="c0fdc-439">`SingleOrDefault`Boş bir koleksiyonda kullanma:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="c0fdc-440">Bir özel durum sonucu oluşur ( `Courses` null başvuru üzerinde bir özellik bulmaya çalışırken).</span><span class="sxs-lookup"><span data-stu-id="c0fdc-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="c0fdc-441">Özel durum iletisi sorunun nedenini daha az gösterir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="c0fdc-442">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="c0fdc-443">*Pages/eğitmenler/Index. cshtml* sayfasının sonuna aşağıdaki biçimlendirmeyi ekleyin Razor :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="c0fdc-444">Yukarıdaki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="c0fdc-445">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-445">Test the app.</span></span> <span data-ttu-id="c0fdc-446">Eğitmenler sayfasında bir **seçme** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="c0fdc-447">Öğrenci verilerini göster</span><span class="sxs-lookup"><span data-stu-id="c0fdc-447">Show student data</span></span>

<span data-ttu-id="c0fdc-448">Bu bölümde, uygulama seçili bir kurs için öğrenci verilerini gösterecek şekilde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="c0fdc-449">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* içindeki yöntemdeki sorguyu aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="c0fdc-450">Güncelleştirme *sayfaları/eğitmenler/Index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="c0fdc-451">Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="c0fdc-452">Yukarıdaki biçimlendirme, seçili kursa kayıtlı öğrencilerin bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="c0fdc-453">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="c0fdc-454">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="c0fdc-456">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="c0fdc-456">Using Single</span></span>

<span data-ttu-id="c0fdc-457">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="c0fdc-458">Yukarıdaki `Single` yaklaşım, kullanarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="c0fdc-459">Bazı geliştiriciler `Single` yaklaşım stilini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="c0fdc-460">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="c0fdc-460">Explicit loading</span></span>

<span data-ttu-id="c0fdc-461">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="c0fdc-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="c0fdc-462">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="c0fdc-463">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="c0fdc-464">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="c0fdc-465">`OnGetAsync`Aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="c0fdc-466">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="c0fdc-467">Bir kurs seçilirse vurgulanan kod şunu alır:</span><span class="sxs-lookup"><span data-stu-id="c0fdc-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="c0fdc-468">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="c0fdc-469">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="c0fdc-470">Yukarıdaki kod açıklamalarının olduğunu fark edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="c0fdc-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="c0fdc-471">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="c0fdc-472">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-472">Test the app.</span></span> <span data-ttu-id="c0fdc-473">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="c0fdc-474">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c0fdc-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0fdc-475">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c0fdc-475">Additional resources</span></span>

* [<span data-ttu-id="c0fdc-476">Bu öğreticinin YouTube sürümü (part1)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="c0fdc-477">Bu öğreticinin YouTube sürümü (part2)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="c0fdc-478">[Önceki](xref:data/ef-rp/complex-data-model) 
> [Sonraki](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="c0fdc-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
