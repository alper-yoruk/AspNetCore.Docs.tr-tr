---
title: 'Bölüm 6, :::no-loc(Razor)::: ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku'
author: rick-anderson
description: :::no-loc(Razor):::Sayfaların 6. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061073"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="3399d-103">Bölüm 6, :::no-loc(Razor)::: ASP.NET Core EF Core olan sayfalar-Ilgili verileri oku</span><span class="sxs-lookup"><span data-stu-id="3399d-103">Part 6, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="3399d-104">, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3399d-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3399d-105">Bu öğreticide, ilgili verilerin nasıl okunacağı ve görüntüleneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3399d-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="3399d-106">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="3399d-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="3399d-107">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="3399d-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="3399d-110">Eager, açık ve yavaş yükleme</span><span class="sxs-lookup"><span data-stu-id="3399d-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="3399d-111">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="3399d-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="3399d-112">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="3399d-113">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="3399d-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="3399d-114">Bir varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="3399d-115">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="3399d-116">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="3399d-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="3399d-117">Birden çok sorgu verme, çok büyük paketlerini tek sorgusundan daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="3399d-118">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="3399d-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="3399d-120">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="3399d-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="3399d-121">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="3399d-121">One query for the main query</span></span> 
  * <span data-ttu-id="3399d-122">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="3399d-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="3399d-123">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="3399d-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="3399d-124">"Düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3399d-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="3399d-125">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="3399d-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="3399d-127">**Note:** EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklara gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="3399d-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="3399d-128">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="3399d-129">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="3399d-130">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="3399d-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3399d-131">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3399d-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="3399d-132">Ayrı sorgularla açık yükleme, veritabanına birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="3399d-133">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3399d-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="3399d-134">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="3399d-135">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3399d-135">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="3399d-137">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3399d-138">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="3399d-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3399d-139">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="3399d-140">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu veritabanına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="3399d-141">Yavaş yükleme performansı zarar verebilir, örneğin, geliştiriciler N + 1 desenler kullanırken, bir üst öğe yüklerken ve alt öğeler aracılığıyla numaralandırıyor.</span><span class="sxs-lookup"><span data-stu-id="3399d-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="3399d-142">Kurs sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="3399d-142">Create Course pages</span></span>

<span data-ttu-id="3399d-143">`Course`Varlık, ilgili varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<span data-ttu-id="3399d-145">Bir kurs için atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="3399d-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="3399d-146">İlgili `Department` varlığı `Course.Department` gezinti özelliğine yükleyin.</span><span class="sxs-lookup"><span data-stu-id="3399d-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="3399d-147">`Department`Varlığın özelliğinden adı alın `Name` .</span><span class="sxs-lookup"><span data-stu-id="3399d-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="3399d-148">Yapı iskelesi kurs sayfaları</span><span class="sxs-lookup"><span data-stu-id="3399d-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3399d-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3399d-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3399d-150">Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="3399d-151">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3399d-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="3399d-152">`Course`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="3399d-153">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3399d-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3399d-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3399d-155">Bir *Sayfalar/kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3399d-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="3399d-156">Kurs sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3399d-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="3399d-157">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="3399d-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="3399d-158">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="3399d-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="3399d-159">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3399d-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="3399d-160">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="3399d-161">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="3399d-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="3399d-162">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="3399d-163">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="3399d-164">Departmanın adını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="3399d-164">Display the department name</span></span>

<span data-ttu-id="3399d-165">Pages/kurslar/Index. cshtml. cs dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="3399d-166">Önceki kod, `Course` özelliği olarak değiştirir `Courses` ve ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="3399d-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="3399d-167">`AsNoTracking` Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="3399d-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="3399d-168">Varlıkların geçerli bağlamda güncelleştirilmediği için izlenmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3399d-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="3399d-169">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3399d-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="3399d-170">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="3399d-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="3399d-171">`Course`Özellik adı olarak değiştirildi `Courses` .</span><span class="sxs-lookup"><span data-stu-id="3399d-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="3399d-172">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="3399d-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="3399d-173">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="3399d-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="3399d-174">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="3399d-175">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="3399d-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="3399d-176">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="3399d-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="3399d-177">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="3399d-179">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="3399d-179">Loading related data with Select</span></span>

<span data-ttu-id="3399d-180">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` .</span><span class="sxs-lookup"><span data-stu-id="3399d-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="3399d-181">`Select`Yöntemi, yalnızca gerekli ilgili verileri yükleyen bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="3399d-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="3399d-182">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="3399d-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="3399d-183">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="3399d-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="3399d-184">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="3399d-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="3399d-185">Yukarıdaki kod hiçbir varlık türü döndürmez, bu nedenle hiçbir izleme yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="3399d-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="3399d-186">EF izleme hakkında daha fazla bilgi için bkz [. Tracking vs. No-Tracking sorguları](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="3399d-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="3399d-187">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="3399d-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="3399d-188">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="3399d-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="3399d-189">Eğitmen sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="3399d-189">Create Instructor pages</span></span>

<span data-ttu-id="3399d-190">Bu bölüm, eğitmen sayfalarını derler ve ilgili Kurslar ve kayıtları eğitmenler dizin sayfasına ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="3399d-191">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="3399d-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="3399d-192">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3399d-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="3399d-193">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="3399d-194">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="3399d-195">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="3399d-196">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="3399d-197">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="3399d-198">Kullanıcı bir eğitmen seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="3399d-199">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="3399d-200">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="3399d-201">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="3399d-202">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3399d-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="3399d-203">Kullanıcı bir kurs seçtiğinde, varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="3399d-204">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="3399d-205">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="3399d-206">Görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="3399d-206">Create a view model</span></span>

<span data-ttu-id="3399d-207">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="3399d-208">Üç tabloyu temsil eden üç özellik içeren bir görünüm modeli gerekir.</span><span class="sxs-lookup"><span data-stu-id="3399d-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="3399d-209">Aşağıdaki kodla *SchoolViewModels/ıncpctorındexdata. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="3399d-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="3399d-210">Yapı iskelesi eğitmeni sayfaları</span><span class="sxs-lookup"><span data-stu-id="3399d-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3399d-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3399d-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3399d-212">Öğrenci sayfalarını aşağıdaki özel durumlarla birlikte [Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) içindeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="3399d-213">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3399d-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="3399d-214">`Instructor`Model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="3399d-215">Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3399d-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3399d-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3399d-217">Bir *sayfa/eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3399d-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="3399d-218">Eğitmen sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="3399d-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="3399d-219">**Windows 'da:**</span><span class="sxs-lookup"><span data-stu-id="3399d-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="3399d-220">**Linux veya macOS 'ta:**</span><span class="sxs-lookup"><span data-stu-id="3399d-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="3399d-221">Yapı iskelesi sayfasının güncelleştirmeden önce nasıl göründüğünü görmek için, uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3399d-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="3399d-222">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="3399d-223">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="3399d-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="3399d-224">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="3399d-225">Kod, aşağıdaki gezinti özellikleri için Eager yüklemeyi belirtir:</span><span class="sxs-lookup"><span data-stu-id="3399d-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="3399d-226">Ve `Include` `ThenInclude` için ve yöntemlerinin yinelendiğine dikkat `CourseAssignments` edin `Course` .</span><span class="sxs-lookup"><span data-stu-id="3399d-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="3399d-227">Bu yineleme, varlığın iki gezinti özelliği için Eager yüklemesi belirtmek için gereklidir `Course` .</span><span class="sxs-lookup"><span data-stu-id="3399d-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="3399d-228">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="3399d-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="3399d-229">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="3399d-230">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="3399d-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="3399d-231">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="3399d-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="3399d-232">Ancak bu durumda, filtre tek bir varlık seçer. bu nedenle, `Single` yöntemi koleksiyonu tek bir varlığa dönüştürmek için çağırılır `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="3399d-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="3399d-233">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="3399d-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="3399d-234">`CourseAssignments` ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="3399d-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="3399d-236">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3399d-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="3399d-237">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3399d-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="3399d-238">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="3399d-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="3399d-239">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="3399d-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="3399d-240">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="3399d-240">Update the instructors Index page</span></span>

<span data-ttu-id="3399d-241">*Pages/eğitmenler/Index. cshtml* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3399d-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="3399d-242">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="3399d-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="3399d-243">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="3399d-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="3399d-244">`"{id:int?}"` bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="3399d-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="3399d-245">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3399d-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="3399d-246">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3399d-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="3399d-247">Sayfa yönergesi olduğunda `@page "{id:int?}"` URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="3399d-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="3399d-248">Yalnızca null değilse görüntülenen bir **Office** sütunu ekler `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="3399d-249">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="3399d-250">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="3399d-251">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="3399d-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="3399d-252">`class="success"` `tr` Seçilen eğitmenin ve kursun öğesine dinamik olarak ekleyen kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="3399d-253">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3399d-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="3399d-254">**Select** etiketli yeni bir köprü ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-254">Adds a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="3399d-255">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3399d-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="3399d-256">Seçili eğitmen için bir kurs tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="3399d-257">Seçili kurs için bir öğrenci kayıtları tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="3399d-258">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="3399d-259">`OfficeAssignment`Null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="3399d-260">Bir eğitmenin **Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3399d-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="3399d-261">Bu eğitmenin atandığı satır stili değişiklikleri ve kurslar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="3399d-262">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="3399d-264">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="3399d-264">Using Single</span></span>

<span data-ttu-id="3399d-265">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="3399d-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="3399d-266">`Single`Bir Where koşulunun kullanımı, kişisel tercihden bağımsız olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3399d-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="3399d-267">Yöntemi kullanılarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="3399d-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="3399d-268">Belirtik yükleme</span><span class="sxs-lookup"><span data-stu-id="3399d-268">Explicit loading</span></span>

<span data-ttu-id="3399d-269">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="3399d-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="3399d-270">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="3399d-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="3399d-271">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3399d-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="3399d-272">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="3399d-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="3399d-273">*Pages/eğitmenler/Index. cshtml. cs* dosyasını aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3399d-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="3399d-274">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="3399d-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="3399d-275">Bir kurs seçilirse, açık yükleme kodu alır:</span><span class="sxs-lookup"><span data-stu-id="3399d-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="3399d-276">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="3399d-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="3399d-277">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="3399d-278">Yukarıdaki kodun yorumdığına dikkat edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="3399d-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="3399d-279">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="3399d-280">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="3399d-280">Test the app.</span></span> <span data-ttu-id="3399d-281">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="3399d-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3399d-282">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3399d-282">Next steps</span></span>

<span data-ttu-id="3399d-283">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3399d-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="3399d-284">[Önceki öğretici](xref:data/ef-rp/complex-data-model) 
> [Sonraki öğretici](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="3399d-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3399d-285">Bu öğreticide ilgili veriler okundu ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="3399d-286">İlgili veriler, EF Core gezinti özelliklerine yüklediği veri.</span><span class="sxs-lookup"><span data-stu-id="3399d-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="3399d-287">Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="3399d-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="3399d-288">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3399d-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3399d-289">Aşağıdaki çizimler, Bu öğreticinin tamamlanan sayfalarını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="3399d-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="3399d-292">İlgili verilerin Eager, açık ve geç yüklemesi</span><span class="sxs-lookup"><span data-stu-id="3399d-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="3399d-293">EF Core bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmenin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="3399d-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="3399d-294">[Eager yükleniyor](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="3399d-295">Ekip yükleme, bir varlık türü için bir sorgu aynı zamanda ilgili varlıkları de yüklediğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="3399d-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="3399d-296">Varlık okunmadığınızda ilgili veriler alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="3399d-297">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="3399d-298">EF Core, bazı Eager yükleme türleri için birden çok sorgu yayımlayacak.</span><span class="sxs-lookup"><span data-stu-id="3399d-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="3399d-299">Birden çok sorgu verilmesi, EF6 içindeki bazı sorgular için tek bir sorgunun bulunduğu durumda daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="3399d-300">Eager yüklemesi ve yöntemleriyle birlikte belirtilir `Include` `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="3399d-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="3399d-302">Bir koleksiyon gezintisi eklendiğinde Eager yüklemesi birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="3399d-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="3399d-303">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="3399d-303">One query for the main query</span></span> 
  * <span data-ttu-id="3399d-304">Yükleme ağacındaki her koleksiyon "Edge" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="3399d-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="3399d-305">Sorguları ile ayır `Load` : veriler ayrı sorgularda alınabilir ve gezinti özellikleri EF Core "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="3399d-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="3399d-306">"düzeltmeler", EF Core gezinti özelliklerini otomatik olarak dolduran anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3399d-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="3399d-307">İle ayrı sorgular `Load` , ek yükleme Eager yüklemeden daha benzer.</span><span class="sxs-lookup"><span data-stu-id="3399d-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="3399d-309">Note: EF Core, daha önce bağlam örneğine yüklenmiş olan diğer varlıklar için gezinti özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="3399d-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="3399d-310">Bir gezinti özelliği için veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse Özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="3399d-311">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="3399d-312">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="3399d-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3399d-313">Gerektiğinde ilgili verileri almak için kodun yazılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3399d-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="3399d-314">Ayrı sorgularla açık yükleme, VERITABANıNA birden çok sorgu gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="3399d-315">Açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3399d-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="3399d-316">`Load`Açık yükleme yapmak için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3399d-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="3399d-317">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3399d-317">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="3399d-319">[Yavaş yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3399d-320">[Sürüm 2,1 ' de EF Core geç yükleme eklendi](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="3399d-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3399d-321">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="3399d-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="3399d-322">Gezinti özelliğine ilk kez erişildiğinde, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="3399d-323">Bir gezinti özelliğine ilk kez erişildiğinde bir sorgu VERITABANıNA gönderilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="3399d-324">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="3399d-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="3399d-325">Bölüm adını görüntüleyen bir kurs sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="3399d-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="3399d-326">Kurs varlığı, varlığı içeren bir gezinti özelliği içerir `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="3399d-327">`Department`Varlık, kursun atandığı departmanı içerir.</span><span class="sxs-lookup"><span data-stu-id="3399d-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="3399d-328">Bir kurs listesinde atanan departmanın adını göstermek için:</span><span class="sxs-lookup"><span data-stu-id="3399d-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="3399d-329">`Name`Varlıktaki özelliği alın `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="3399d-330">`Department`Varlık, `Course.Department` Gezinti özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="3399d-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Kurs. Departmanı](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="3399d-332">Kurs modelini temklesi</span><span class="sxs-lookup"><span data-stu-id="3399d-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3399d-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3399d-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="3399d-334">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Course` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="3399d-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3399d-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3399d-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="3399d-336">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3399d-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="3399d-337">Yukarıdaki komut, modeli bir daha yasaklıyor `Course` .</span><span class="sxs-lookup"><span data-stu-id="3399d-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="3399d-338">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="3399d-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="3399d-339">*Pages/kurslar/Index. cshtml. cs* dosyasını açın ve `OnGetAsync` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3399d-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="3399d-340">Yapı iskelesi altyapısı, gezinti özelliği için belirtilen Eager 'yi belirtti `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="3399d-341">`Include`Yöntemi Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="3399d-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="3399d-342">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="3399d-343">Departman sütunu, `DepartmentID` yararlı olmayan öğesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="3399d-344">`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="3399d-345">Yukarıdaki kod ekler `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="3399d-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="3399d-346">`AsNoTracking` Döndürülen varlıklar izlenmediğinden performansı geliştirir.</span><span class="sxs-lookup"><span data-stu-id="3399d-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="3399d-347">Geçerli bağlamda güncelleştirilmemiş oldukları için varlıklar izlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="3399d-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="3399d-348">*Pages/kurslar/Index. cshtml* 'yi aşağıdaki vurgulanmış işaretlerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="3399d-349">Yapı iskelesi kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="3399d-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="3399d-350">Başlık dizinden kurslar olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="3399d-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="3399d-351">Özellik değerini gösteren bir **sayı** sütunu eklendi `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="3399d-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="3399d-352">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="3399d-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="3399d-353">Ancak, bu durumda birincil anahtar anlamlı olur.</span><span class="sxs-lookup"><span data-stu-id="3399d-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="3399d-354">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="3399d-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="3399d-355">Kod, `Name` `Department` gezinti özelliğine yüklenen varlığın özelliğini görüntüler `Department` :</span><span class="sxs-lookup"><span data-stu-id="3399d-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="3399d-356">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="3399d-358">Select ile ilgili verileri yükleme</span><span class="sxs-lookup"><span data-stu-id="3399d-358">Loading related data with Select</span></span>

<span data-ttu-id="3399d-359">`OnGetAsync`Yöntemi, yöntemiyle ilgili verileri yükler `Include` :</span><span class="sxs-lookup"><span data-stu-id="3399d-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="3399d-360">`Select`İşleci yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="3399d-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="3399d-361">Tek öğeler için, örneğin `Department.Name` BIR SQL Iç birleşimi kullanır.</span><span class="sxs-lookup"><span data-stu-id="3399d-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="3399d-362">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak `Include` koleksiyonlar üzerindeki işleci bu şekilde yapar.</span><span class="sxs-lookup"><span data-stu-id="3399d-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="3399d-363">Aşağıdaki kod, yöntemiyle ilgili verileri yükler `Select` :</span><span class="sxs-lookup"><span data-stu-id="3399d-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="3399d-364">`CourseViewModel`Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="3399d-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="3399d-365">Tüm örnek için bkz. [ındexselect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="3399d-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="3399d-366">Kurslar ve kayıtları gösteren bir eğitmenler sayfası oluşturun</span><span class="sxs-lookup"><span data-stu-id="3399d-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="3399d-367">Bu bölümde, Eğitmenler sayfası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3399d-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="3399d-368">![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="3399d-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="3399d-369">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="3399d-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="3399d-370">Eğitmenler listesi, varlıktaki ilgili verileri `OfficeAssignment` (önceki görüntüde Office) görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="3399d-371">`Instructor`Ve `OfficeAssignment` varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="3399d-372">Varlıklar için Eager yüklemesi kullanılır `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="3399d-373">Eager yüklemesi, ilgili verilerin görüntülenmesi gerektiğinde genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="3399d-374">Bu durumda, Eğitmenler için Office atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="3399d-375">Kullanıcı bir eğitmen (önceki görüntüde Haruı) seçtiğinde ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="3399d-376">`Instructor`Ve `Course` varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="3399d-377">Eager yüklemesi, `Course` varlıklar ve ilgili varlıklar için kullanılır `Department` .</span><span class="sxs-lookup"><span data-stu-id="3399d-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="3399d-378">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="3399d-379">Bu örnek, gezinti özelliklerinde olan varlıklarda gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3399d-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="3399d-380">Kullanıcı bir kurs seçtiğinde (önceki görüntüde Chemistry), varlıktaki ilgili veriler `Enrollments` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="3399d-381">Önceki görüntüde öğrenci adı ve sınıf görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="3399d-382">`Course`Ve `Enrollment` varlıkları bire çok ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="3399d-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="3399d-383">Eğitmen dizini görünümü için bir görünüm modeli oluşturun</span><span class="sxs-lookup"><span data-stu-id="3399d-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="3399d-384">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="3399d-385">Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3399d-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="3399d-386">*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="3399d-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="3399d-387">Eğitmen modelini scafkatlama</span><span class="sxs-lookup"><span data-stu-id="3399d-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3399d-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3399d-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="3399d-389">[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-the-student-model) ve `Instructor` model sınıfı için kullanma bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="3399d-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3399d-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3399d-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="3399d-391">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3399d-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="3399d-392">Yukarıdaki komut, modeli bir daha yasaklıyor `Instructor` .</span><span class="sxs-lookup"><span data-stu-id="3399d-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="3399d-393">Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3399d-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="3399d-394">*Pages/eğitmenler/Index. cshtml. cs* öğesini şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="3399d-395">`OnGetAsync`Yöntemi, seçilen EĞITMENIN kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="3399d-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="3399d-396">*Pages/eğitmenler/Index. cshtml. cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="3399d-397">Sorgunun iki içerme vardır:</span><span class="sxs-lookup"><span data-stu-id="3399d-397">The query has two includes:</span></span>

* <span data-ttu-id="3399d-398">`OfficeAssignment`: [Eğitmenler görünümünde](#IP)görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="3399d-399">`CourseAssignments`: Kurslar taöğretme.</span><span class="sxs-lookup"><span data-stu-id="3399d-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="3399d-400">Eğitmenler Dizin sayfasını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="3399d-400">Update the instructors Index page</span></span>

<span data-ttu-id="3399d-401">*Sayfaları/eğitmenler/Index. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="3399d-402">Yukarıdaki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="3399d-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="3399d-403">`page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="3399d-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="3399d-404">`"{id:int?}"` bir yol şablonudur.</span><span class="sxs-lookup"><span data-stu-id="3399d-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="3399d-405">Yol şablonu, verileri yönlendirmek için URL 'deki tamsayı Sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3399d-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="3399d-406">Örneğin, yalnızca yönergeyle bir eğitmenin **Select** bağlantısına tıklanması `@page` AŞAĞıDAKINE benzer bir URL oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3399d-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="3399d-407">Sayfa yönergesi olduğunda `@page "{id:int?}"` , ÖNCEKI URL şu şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="3399d-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="3399d-408">Sayfa başlığı **eğitmenler** ' dir.</span><span class="sxs-lookup"><span data-stu-id="3399d-408">Page title is **Instructors** .</span></span>
* <span data-ttu-id="3399d-409">Yalnızca null olmaması halinde görüntülenen bir **Office** sütunu eklendi `item.OfficeAssignment.Location` `item.OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="3399d-410">Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="3399d-411">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="3399d-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="3399d-412">Bu Razor sözdizimi hakkında daha fazla bilgi için bkz. [açık hat geçişi](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="3399d-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="3399d-413">`class="success"`Seçilen eğitmenin öğesine dinamik olarak ekleyen kod eklendi `tr` .</span><span class="sxs-lookup"><span data-stu-id="3399d-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="3399d-414">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3399d-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="3399d-415">**Select** etiketli yeni bir köprü eklendi.</span><span class="sxs-lookup"><span data-stu-id="3399d-415">Added a new hyperlink labeled **Select** .</span></span> <span data-ttu-id="3399d-416">Bu bağlantı, seçilen eğitmenin KIMLIĞINI `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3399d-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="3399d-417">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa, `Location` ilgili varlıktaki (Office) öğesini görüntüler `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="3399d-418">Eğer OfficeAssignment ' null ise boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3399d-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="3399d-419">**Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3399d-419">Click on the **Select** link.</span></span> <span data-ttu-id="3399d-420">Satır stili değişir.</span><span class="sxs-lookup"><span data-stu-id="3399d-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="3399d-421">Seçili eğitmen 'e göre kurslar ekleyin</span><span class="sxs-lookup"><span data-stu-id="3399d-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="3399d-422">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* dosyasındaki yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="3399d-423">Ekleyemiyorum `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="3399d-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="3399d-424">Güncelleştirilmiş sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="3399d-425">Önceki sorgu `Department` varlıkları ekler.</span><span class="sxs-lookup"><span data-stu-id="3399d-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="3399d-426">Aşağıdaki kod, bir eğitmen seçildiğinde ( `id != null` ) yürütülür.</span><span class="sxs-lookup"><span data-stu-id="3399d-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="3399d-427">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="3399d-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="3399d-428">Görünüm modelinin özelliği, `Courses` `Course` bu eğitmenin gezinti özelliğinden alınan varlıklarla birlikte yüklenir `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="3399d-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="3399d-429">`Where`Yöntemi bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="3399d-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="3399d-430">Önceki `Where` yöntemde yalnızca tek bir `Instructor` varlık döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3399d-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="3399d-431">`Single`Yöntemi, koleksiyonu tek bir `Instructor` varlığa dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3399d-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="3399d-432">`Instructor`Varlık, özelliğine erişim sağlar `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="3399d-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="3399d-433">`CourseAssignments` ilgili varlıklara erişim sağlar `Course` .</span><span class="sxs-lookup"><span data-stu-id="3399d-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Eğitmenden kurslar M:d](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="3399d-435">`Single`Koleksiyonda yalnızca bir öğe olduğunda yöntem bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3399d-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="3399d-436">`Single`Koleksiyon boşsa veya birden fazla öğe varsa Yöntem bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3399d-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="3399d-437">`SingleOrDefault`Koleksiyon boşsa varsayılan bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="3399d-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="3399d-438">`SingleOrDefault`Boş bir koleksiyonda kullanma:</span><span class="sxs-lookup"><span data-stu-id="3399d-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="3399d-439">Bir özel durum sonucu oluşur ( `Courses` null başvuru üzerinde bir özellik bulmaya çalışırken).</span><span class="sxs-lookup"><span data-stu-id="3399d-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="3399d-440">Özel durum iletisi sorunun nedenini daha az gösterir.</span><span class="sxs-lookup"><span data-stu-id="3399d-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="3399d-441">Aşağıdaki kod, `Enrollments` bir kurs seçildiğinde görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="3399d-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="3399d-442">*Pages/eğitmenler/Index. cshtml* sayfasının sonuna aşağıdaki biçimlendirmeyi ekleyin :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="3399d-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="3399d-443">Yukarıdaki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="3399d-444">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="3399d-444">Test the app.</span></span> <span data-ttu-id="3399d-445">Eğitmenler sayfasında bir **seçme** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3399d-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="3399d-446">Öğrenci verilerini göster</span><span class="sxs-lookup"><span data-stu-id="3399d-446">Show student data</span></span>

<span data-ttu-id="3399d-447">Bu bölümde, uygulama seçili bir kurs için öğrenci verilerini gösterecek şekilde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="3399d-448">`OnGetAsync` *Pages/eğitmenler/Index. cshtml. cs* içindeki yöntemdeki sorguyu aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="3399d-449">Güncelleştirme *sayfaları/eğitmenler/Index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3399d-449">Update *Pages/Instructors/Index.cshtml* .</span></span> <span data-ttu-id="3399d-450">Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3399d-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="3399d-451">Yukarıdaki biçimlendirme, seçili kursa kayıtlı öğrencilerin bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3399d-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="3399d-452">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="3399d-453">Kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="3399d-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="3399d-455">Tek kullanımı</span><span class="sxs-lookup"><span data-stu-id="3399d-455">Using Single</span></span>

<span data-ttu-id="3399d-456">`Single`Yöntemi `Where` yöntemi ayrı çağırmak yerine koşulu geçirebilir `Where` :</span><span class="sxs-lookup"><span data-stu-id="3399d-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="3399d-457">Yukarıdaki `Single` yaklaşım, kullanarak herhangi bir avantaj sağlamaz `Where` .</span><span class="sxs-lookup"><span data-stu-id="3399d-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="3399d-458">Bazı geliştiriciler `Single` yaklaşım stilini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="3399d-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="3399d-459">Belirtik yükleme</span><span class="sxs-lookup"><span data-stu-id="3399d-459">Explicit loading</span></span>

<span data-ttu-id="3399d-460">Geçerli kod ve için Eager yüklemeyi belirtir `Enrollments` `Students` :</span><span class="sxs-lookup"><span data-stu-id="3399d-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="3399d-461">Kullanıcıların bir kursa kayıtları nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="3399d-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="3399d-462">Bu durumda, bir iyileştirme yalnızca isteniyorsa kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3399d-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="3399d-463">Bu bölümde, ve ' `OnGetAsync` nin açık yüklemesini kullanacak şekilde güncelleştirilir `Enrollments` `Students` .</span><span class="sxs-lookup"><span data-stu-id="3399d-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="3399d-464">`OnGetAsync`Aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3399d-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="3399d-465">Yukarıdaki kod, kayıt ve öğrenci verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="3399d-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="3399d-466">Bir kurs seçilirse vurgulanan kod şunu alır:</span><span class="sxs-lookup"><span data-stu-id="3399d-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="3399d-467">`Enrollment`Seçili kurs için varlıklar.</span><span class="sxs-lookup"><span data-stu-id="3399d-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="3399d-468">`Student`Her biri için varlıklar `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="3399d-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="3399d-469">Yukarıdaki kod açıklamalarının olduğunu fark edin `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="3399d-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="3399d-470">Gezinti özellikleri yalnızca izlenen varlıklar için açık bir şekilde yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3399d-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="3399d-471">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="3399d-471">Test the app.</span></span> <span data-ttu-id="3399d-472">Bir kullanıcının perspektifinden, uygulama önceki sürümle aynı şekilde davranır.</span><span class="sxs-lookup"><span data-stu-id="3399d-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="3399d-473">Sonraki öğreticide ilgili verileri güncelleştirme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="3399d-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3399d-474">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3399d-474">Additional resources</span></span>

* [<span data-ttu-id="3399d-475">Bu öğreticinin YouTube sürümü (part1)</span><span class="sxs-lookup"><span data-stu-id="3399d-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="3399d-476">Bu öğreticinin YouTube sürümü (part2)</span><span class="sxs-lookup"><span data-stu-id="3399d-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="3399d-477">[Önceki](xref:data/ef-rp/complex-data-model) 
> [Sonraki](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="3399d-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
