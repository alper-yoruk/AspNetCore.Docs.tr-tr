---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - İlgili Verileri Oku - 8'de 6
author: rick-anderson
description: Bu öğreticide, ilgili verileri , yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri okuyup görüntülersiniz.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 0835dbf6b5434e4224d9f56ea3335df4b5d9e119
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277333"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="ce770-103">ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - İlgili Verileri Oku - 8'de 6</span><span class="sxs-lookup"><span data-stu-id="ce770-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="ce770-104">Yazar: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ce770-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce770-105">Bu öğretici, ilgili verilerin nasıl okunup görüntülenebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="ce770-106">İlgili veriler, EF Core'un gezinme özelliklerine yüklediğini gösteren verilerdir.</span><span class="sxs-lookup"><span data-stu-id="ce770-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ce770-107">Aşağıdaki çizimler bu öğretici için tamamlanmış sayfaları gösterir:</span><span class="sxs-lookup"><span data-stu-id="ce770-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index30.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="ce770-110">Istekli, açık ve tembel yükleme</span><span class="sxs-lookup"><span data-stu-id="ce770-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="ce770-111">EF Core'un ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:</span><span class="sxs-lookup"><span data-stu-id="ce770-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ce770-112">[Istekli yükleme](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ce770-113">Bir varlık türü için yapılan bir sorgu, ilgili varlıkları da yüklerken, istekli yüklemedir.</span><span class="sxs-lookup"><span data-stu-id="ce770-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ce770-114">Bir varlık okunduğunda, ilgili verileri alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ce770-115">Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ce770-116">EF Core, bazı istekli yükleme türleri için birden çok sorgu yayımlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ce770-117">Birden çok sorgu vermek, dev tek bir sorgudan daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="ce770-118">Istekli yükleme `Include` ve `ThenInclude` yöntemleri ile belirtilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ce770-120">Bir koleksiyon gezintisi dahil edildiğinde istekli yükleme birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="ce770-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ce770-121">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="ce770-121">One query for the main query</span></span> 
  * <span data-ttu-id="ce770-122">Yük ağacındaki her koleksiyon "kenar" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="ce770-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ce770-123">Ayrı sorgular `Load`: Veriler ayrı sorgularda alınabilir ve EF Core gezinti özelliklerini "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="ce770-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ce770-124">"Düzeltmeler" EF Core'un gezinme özelliklerini otomatik olarak doldurdığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ce770-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ce770-125">Ayrı sorgular, `Load` istekli yüklemeden çok açık yüklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="ce770-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ce770-127">Not: EF Core, daha önce bağlam örneğine yüklenen diğer varlıklara gezinme özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="ce770-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ce770-128">Bir gezinti özelliğine ait veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse, özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ce770-129">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ce770-130">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ce770-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ce770-131">Gerektiğinde ilgili verileri almak için kod yazılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ce770-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ce770-132">Ayrı sorgularla açık yükleme, veritabanına gönderilen birden çok sorguyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="ce770-133">Açık yükleme yle, kod yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce770-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ce770-134">Açık `Load` yükleme yapmak için yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ce770-135">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ce770-135">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ce770-137">[Tembel yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ce770-138">[Pİ Core'a sürüm 2.1'de tembel yükleme eklendi.](/ef/core/querying/related-data#lazy-loading)</span><span class="sxs-lookup"><span data-stu-id="ce770-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ce770-139">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ce770-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ce770-140">Bir gezinti özelliğine ilk erişici olduğunda, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ce770-141">Bir gezinti özelliğine ilk kez her erişici olduğunda veritabanına bir sorgu gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="ce770-142">Kurs sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-142">Create Course pages</span></span>

<span data-ttu-id="ce770-143">Varlık, `Course` ilgili `Department` varlığı içeren bir gezinti özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="ce770-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Kurs.Bölüm](read-related-data/_static/dep-crs.png)

<span data-ttu-id="ce770-145">Bir kurs için atanan bölümün adını görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="ce770-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="ce770-146">İlgili `Department` varlığı gezinti `Course.Department` özelliğine yükleyin.</span><span class="sxs-lookup"><span data-stu-id="ce770-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="ce770-147">Adı varlığın `Department` `Name` mülkünden alın.</span><span class="sxs-lookup"><span data-stu-id="ce770-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="ce770-148">İskele Kursu sayfaları</span><span class="sxs-lookup"><span data-stu-id="ce770-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce770-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce770-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ce770-150">Aşağıdaki istisnalar dışında [İskele Öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ce770-151">*Sayfalar/Kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ce770-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="ce770-152">Model `Course` sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="ce770-153">Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce770-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce770-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ce770-155">*Sayfalar/Kurslar* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ce770-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="ce770-156">Kurs sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ce770-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="ce770-157">**Windows'da:**</span><span class="sxs-lookup"><span data-stu-id="ce770-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="ce770-158">**Linux veya macOS'ta:**</span><span class="sxs-lookup"><span data-stu-id="ce770-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="ce770-159">*Sayfaları/Dersleri/Index.cshtml.cs'yi* açın `OnGetAsync` ve yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ce770-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ce770-160">İskele motoru `Department` navigasyon özelliği için istekli yükleme belirtti.</span><span class="sxs-lookup"><span data-stu-id="ce770-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ce770-161">Yöntem, `Include` istekli yükleme yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce770-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="ce770-162">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ce770-163">Bölüm sütunu `DepartmentID`, hangi yararlı değildir görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="ce770-164">Bölüm adını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="ce770-164">Display the department name</span></span>

<span data-ttu-id="ce770-165">Sayfaları/Dersleri/Index.cshtml.cs'yi aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="ce770-166">Önceki kod `Course` özelliği değiştirir `Courses` ve `AsNoTracking`ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="ce770-167">`AsNoTracking`döndürülen varlıklar izlenmediği için performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="ce770-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ce770-168">Varlıkların izlenmeleri gerekmez, çünkü geçerli bağlamda güncelleştirilmezler.</span><span class="sxs-lookup"><span data-stu-id="ce770-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ce770-169">*Sayfaları/Kursları/Index.cshtml'i* aşağıdaki kodla güncelleyin.</span><span class="sxs-lookup"><span data-stu-id="ce770-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="ce770-170">İskele kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="ce770-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ce770-171">`Course` Özellik adını ' `Courses`da değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="ce770-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="ce770-172">Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID`</span><span class="sxs-lookup"><span data-stu-id="ce770-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ce770-173">Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez.</span><span class="sxs-lookup"><span data-stu-id="ce770-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ce770-174">Ancak, bu durumda birincil anahtar anlamlıdır.</span><span class="sxs-lookup"><span data-stu-id="ce770-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ce770-175">Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="ce770-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ce770-176">Kod, gezinti `Name` özelliğine `Department` yüklenen varlığın özelliğini `Department` görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ce770-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ce770-177">Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ce770-179">İlgili verileri Select ile yükleme</span><span class="sxs-lookup"><span data-stu-id="ce770-179">Loading related data with Select</span></span>

<span data-ttu-id="ce770-180">Yöntem, `OnGetAsync` ilgili verileri `Include` yöntemle yükler.</span><span class="sxs-lookup"><span data-stu-id="ce770-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="ce770-181">Yöntem, `Select` yalnızca gerekli ilgili verileri yükleyen bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="ce770-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="ce770-182">Tek öğeler için, `Department.Name` bir SQL INNER JOIN kullanır gibi.</span><span class="sxs-lookup"><span data-stu-id="ce770-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ce770-183">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak koleksiyonlar üzerindeki `Include` işleç de kullanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ce770-184">Aşağıdaki kod `Select` yöntemle ilgili verileri yükler:</span><span class="sxs-lookup"><span data-stu-id="ce770-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="ce770-185">Önceki kod herhangi bir varlık türü döndürmez, bu nedenle hiçbir izleme yapılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="ce770-186">EF izleme hakkında daha fazla bilgi için Izleme [ve İzleme Sorgusu Yok'a](/ef/core/querying/tracking)bakın.</span><span class="sxs-lookup"><span data-stu-id="ce770-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="ce770-187">Ne `CourseViewModel`var:</span><span class="sxs-lookup"><span data-stu-id="ce770-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ce770-188">Tam bir örnek için [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) bakın.</span><span class="sxs-lookup"><span data-stu-id="ce770-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="ce770-189">Eğitmen sayfaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-189">Create Instructor pages</span></span>

<span data-ttu-id="ce770-190">Bu bölüm, Eğitmen sayfalarını iskeleler ve ilgili Dersler ve Kayıtlar'ı Eğitmenler Endeksi sayfasına ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="ce770-191">![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="ce770-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="ce770-192">Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ce770-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ce770-193">Eğitmenlerlistesi `OfficeAssignment` varlıktan (Önceki resimdeki Office) ilgili verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ce770-194">`OfficeAssignment` Varlıklar `Instructor` birden sıfıra veya bir ilişkisindedir.</span><span class="sxs-lookup"><span data-stu-id="ce770-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ce770-195">Araçlar için `OfficeAssignment` istekli yükleme kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ce770-196">İlgili verilerin görüntülenmesi gerektiğinde istekli yükleme genellikle daha verimlidir.</span><span class="sxs-lookup"><span data-stu-id="ce770-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ce770-197">Bu durumda, eğitmenler için ofis atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ce770-198">Kullanıcı bir eğitmen seçtiğinde, `Course` ilgili varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="ce770-199">Ve `Instructor` `Course` varlıklar çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ce770-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ce770-200">Istekli yükleme `Course` varlıklar ve ilgili `Department` varlıklar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ce770-201">Bu durumda, yalnızca seçilen eğitmen için kurslar gerektiğinden, ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ce770-202">Bu örnek, gezinti özellikleri olan varlıklarda gezinme özellikleri için istekli yüklemenin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ce770-203">Kullanıcı bir kurs seçtiğinde, `Enrollments` varlıktan ilgili veriler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ce770-204">Bir önceki resimde öğrenci adı ve notu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ce770-205">Ve `Course` `Enrollment` varlıklar bir-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ce770-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="ce770-206">Görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-206">Create a view model</span></span>

<span data-ttu-id="ce770-207">Eğitmenler sayfası üç farklı tablodan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ce770-208">Üç tabloyu temsil eden üç özelliği içeren bir görünüm modeli gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ce770-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="ce770-209">Aşağıdaki kodile *SchoolViewModels/InstructorIndexData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ce770-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="ce770-210">İskele Eğitmeni sayfaları</span><span class="sxs-lookup"><span data-stu-id="ce770-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce770-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce770-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ce770-212">Aşağıdaki istisnalar dışında [öğrenci sayfalarını İskele'deki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="ce770-213">*Sayfalar/Eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ce770-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="ce770-214">Model `Instructor` sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="ce770-215">Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce770-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce770-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ce770-217">*Sayfalar/Eğitmenler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ce770-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="ce770-218">Eğitmen sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="ce770-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="ce770-219">**Windows'da:**</span><span class="sxs-lookup"><span data-stu-id="ce770-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="ce770-220">**Linux veya macOS'ta:**</span><span class="sxs-lookup"><span data-stu-id="ce770-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ce770-221">İskeleli sayfayı güncellemeden önce nasıl göründüğünü görmek için uygulamayı çalıştırın ve Eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ce770-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="ce770-222">*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="ce770-223">Yöntem, `OnGetAsync` seçili eğitmenin kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ce770-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ce770-224">*Sayfalar/Eğitmenler/Index.cshtml.cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="ce770-225">Kod, aşağıdaki gezinme özellikleri için istekli yüklemeyi belirtir:</span><span class="sxs-lookup"><span data-stu-id="ce770-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="ce770-226">Tekrarı `Include` ve `ThenInclude` yöntemleri dikkat `CourseAssignments` `Course`edin ve .</span><span class="sxs-lookup"><span data-stu-id="ce770-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="ce770-227">Bu yineleme, varlığın `Course` iki gezinti özelliği için istekli yükleme yi belirtmek için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="ce770-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="ce770-228">Bir eğitmen seçildiğinde aşağıdaki kod`id != null`çalıştırılır ( ).</span><span class="sxs-lookup"><span data-stu-id="ce770-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="ce770-229">Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ce770-230">Görünüm modelinin `Courses` özelliği, o `Course` eğitmenin `CourseAssignments` gezinti özelliğinden gelen varlıklarla yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="ce770-231">Yöntem `Where` bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="ce770-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="ce770-232">Ancak bu durumda, filtre tek bir varlık seçer.</span><span class="sxs-lookup"><span data-stu-id="ce770-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="ce770-233">bu `Single` nedenle yöntem, koleksiyonu tek `Instructor` bir varlığa dönüştürmek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ce770-234">`CourseAssignments` Varlık, `Instructor` özelliğe erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ce770-235">`CourseAssignments`ilgili `Course` varlıklara erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ce770-237">Yöntem, `Single` koleksiyonda yalnızca bir öğe olduğunda bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ce770-238">Yöntem, `Single` koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ce770-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ce770-239">Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir.</span><span class="sxs-lookup"><span data-stu-id="ce770-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="ce770-240">Bir kurs seçildiğinde aşağıdaki kod `Enrollments` görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="ce770-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ce770-241">Eğitmenlerin Dizin sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ce770-241">Update the instructors Index page</span></span>

<span data-ttu-id="ce770-242">*Sayfaları/Eğitmenleri/Index.cshtml'i* aşağıdaki kodla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ce770-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="ce770-243">Önceki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ce770-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ce770-244">Yönergeyi `page` `@page` `@page "{id:int?}"`' den .</span><span class="sxs-lookup"><span data-stu-id="ce770-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ce770-245">`"{id:int?}"`bir rota şablonudur.</span><span class="sxs-lookup"><span data-stu-id="ce770-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ce770-246">Rota şablonu, verileri yönlendirmek için URL'deki sonda sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ce770-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ce770-247">Örneğin, yalnızca yönergesi `@page` olan bir eğitmen için **Seç** bağlantısını tıklattığınızda aşağıdaki gibi bir URL üretir:</span><span class="sxs-lookup"><span data-stu-id="ce770-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="ce770-248">Sayfa yönergesi url olduğunda: `@page "{id:int?}"`</span><span class="sxs-lookup"><span data-stu-id="ce770-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="ce770-249">Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ce770-250">Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment varlığı olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ce770-251">Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ce770-252">Bu jilet sözdizimi hakkında daha fazla şey için [Açık satır geçişine](xref:mvc/views/razor#explicit-line-transition) bakın.</span><span class="sxs-lookup"><span data-stu-id="ce770-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="ce770-253">Seçilen eğitmen ve `class="success"` kursun `tr` öğesine dinamik olarak ekleyen kod ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="ce770-254">Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ce770-255">**Seç**etiketli yeni bir köprü ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ce770-256">Bu bağlantı, seçilen eğitmenin kimliğini `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="ce770-257">Seçilen Eğitmen için bir kurs tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="ce770-258">Seçilen kurs için öğrenci kayıtları tablosu ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="ce770-259">Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili `Location` `OfficeAssignment` varlığın (ofis) görüntüleniyor.</span><span class="sxs-lookup"><span data-stu-id="ce770-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ce770-260">Null `OfficeAssignment` ise, boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ce770-261">Eğitmen için **Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ce770-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="ce770-262">Satır stili değişiklikleri ve bu eğitmene atanan dersler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="ce770-263">Kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="ce770-265">Tek Kullanma</span><span class="sxs-lookup"><span data-stu-id="ce770-265">Using Single</span></span>

<span data-ttu-id="ce770-266">Yöntem, `Single` `Where` yöntemi ayrı `Where` ayrı çağırmak yerine koşulda geçebilir:</span><span class="sxs-lookup"><span data-stu-id="ce770-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ce770-267">Bir `Single` Nerede koşulu kişisel tercih meselesi ile kullanımı.</span><span class="sxs-lookup"><span data-stu-id="ce770-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="ce770-268">Bu `Where` yöntemi kullanarak üzerinde hiçbir fayda sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ce770-269">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="ce770-269">Explicit loading</span></span>

<span data-ttu-id="ce770-270">Geçerli kod için `Enrollments` istekli yükleme `Students`belirtir ve:</span><span class="sxs-lookup"><span data-stu-id="ce770-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="ce770-271">Kullanıcıların bir kurstaki kayıtları nadiren görmek istediklerini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="ce770-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ce770-272">Bu durumda, en iyi duruma getirilmesi yalnızca istenirse kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ce770-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ce770-273">Bu bölümde, `OnGetAsync` açık yükleme `Enrollments` ve kullanmak `Students`için güncellenir .</span><span class="sxs-lookup"><span data-stu-id="ce770-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ce770-274">*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla güncelleyin.</span><span class="sxs-lookup"><span data-stu-id="ce770-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="ce770-275">Önceki kod, Kayıt ve öğrenci verileri için *ThenInclude* yöntemini düşürür.</span><span class="sxs-lookup"><span data-stu-id="ce770-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ce770-276">Bir kurs seçilirse, açık yükleme kodu alır:</span><span class="sxs-lookup"><span data-stu-id="ce770-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="ce770-277">`Enrollment` Seçilen kursun varlıkları.</span><span class="sxs-lookup"><span data-stu-id="ce770-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ce770-278">Her `Student` biri `Enrollment`için varlıklar .</span><span class="sxs-lookup"><span data-stu-id="ce770-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ce770-279">Önceki kodun dışarı yorum `.AsNoTracking()`yaptığına dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="ce770-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ce770-280">Gezinti özellikleri yalnızca izlenen varlıklar için açıkça yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ce770-281">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="ce770-281">Test the app.</span></span> <span data-ttu-id="ce770-282">Bir kullanıcının bakış açısından, uygulama önceki sürümle aynı şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="ce770-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ce770-283">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="ce770-283">Next steps</span></span>

<span data-ttu-id="ce770-284">Sonraki öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="ce770-285">[Önceki öğretici](xref:data/ef-rp/complex-data-model)
>[Sonraki öğretici](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ce770-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce770-286">Bu öğreticide, ilgili veriler okunur ve görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="ce770-287">İlgili veriler, EF Core'un gezinme özelliklerine yüklediğini gösteren verilerdir.</span><span class="sxs-lookup"><span data-stu-id="ce770-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="ce770-288">Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="ce770-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="ce770-289">[Talimatları indirin.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ce770-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="ce770-290">Aşağıdaki çizimler bu öğretici için tamamlanmış sayfaları gösterir:</span><span class="sxs-lookup"><span data-stu-id="ce770-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="ce770-293">İlgili verilerin istekli, açık ve tembel yüklenmesi</span><span class="sxs-lookup"><span data-stu-id="ce770-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="ce770-294">EF Core'un ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:</span><span class="sxs-lookup"><span data-stu-id="ce770-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="ce770-295">[Istekli yükleme](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="ce770-296">Bir varlık türü için yapılan bir sorgu, ilgili varlıkları da yüklerken, istekli yüklemedir.</span><span class="sxs-lookup"><span data-stu-id="ce770-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="ce770-297">Varlık okunduğunda, ilgili verileri alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="ce770-298">Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="ce770-299">EF Core, bazı istekli yükleme türleri için birden çok sorgu yayımlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="ce770-300">Birden çok sorgu verme, tek bir sorgunun bulunduğu EF6'daki bazı sorgularda olduğundan daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="ce770-301">Istekli yükleme `Include` ve `ThenInclude` yöntemleri ile belirtilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="ce770-303">Bir koleksiyon gezintisi dahil edildiğinde istekli yükleme birden çok sorgu gönderir:</span><span class="sxs-lookup"><span data-stu-id="ce770-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="ce770-304">Ana sorgu için bir sorgu</span><span class="sxs-lookup"><span data-stu-id="ce770-304">One query for the main query</span></span> 
  * <span data-ttu-id="ce770-305">Yük ağacındaki her koleksiyon "kenar" için bir sorgu.</span><span class="sxs-lookup"><span data-stu-id="ce770-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="ce770-306">Ayrı sorgular `Load`: Veriler ayrı sorgularda alınabilir ve EF Core gezinti özelliklerini "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="ce770-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="ce770-307">"düzeltmeler", EF Core'un gezinme özelliklerini otomatik olarak doldurdığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="ce770-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="ce770-308">Ayrı sorgular, `Load` istekli yüklemeden çok açık yüklemeye benzer.</span><span class="sxs-lookup"><span data-stu-id="ce770-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="ce770-310">Not: EF Core, daha önce bağlam örneğine yüklenen diğer varlıklara gezinme özelliklerini otomatik olarak düzeltir.</span><span class="sxs-lookup"><span data-stu-id="ce770-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="ce770-311">Bir gezinti özelliğine ait veriler açıkça dahil *edilmese* bile, ilgili varlıkların bazıları veya tümü daha önce yüklenmişse, özellik yine de doldurulabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="ce770-312">[Açık yükleme](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="ce770-313">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ce770-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ce770-314">Gerektiğinde ilgili verileri almak için kod yazılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ce770-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="ce770-315">Ayrı sorgularla açık yükleme, DB'ye gönderilen birden çok sorguyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="ce770-316">Açık yükleme yle, kod yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce770-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="ce770-317">Açık `Load` yükleme yapmak için yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="ce770-318">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ce770-318">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="ce770-320">[Tembel yükleme](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="ce770-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ce770-321">[Pİ Core'a sürüm 2.1'de tembel yükleme eklendi.](/ef/core/querying/related-data#lazy-loading)</span><span class="sxs-lookup"><span data-stu-id="ce770-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="ce770-322">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="ce770-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="ce770-323">Bir gezinti özelliğine ilk erişici olduğunda, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="ce770-324">Bir gezinti özelliğine ilk kez her erişir'de DB'ye bir sorgu gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="ce770-325">Operatör `Select` yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="ce770-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="ce770-326">Bölüm adını görüntüleyen bir Kurs sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="ce770-327">Kurs varlığı, `Department` varlığı içeren bir gezinti özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="ce770-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="ce770-328">Kuruluş, `Department` kursun atandığı bölümü içerir.</span><span class="sxs-lookup"><span data-stu-id="ce770-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="ce770-329">Atanan bölümün adını bir ders listesinde görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="ce770-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="ce770-330">Varlığın `Name` mülkiyetini `Department` alın.</span><span class="sxs-lookup"><span data-stu-id="ce770-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="ce770-331">Varlık `Department` `Course.Department` navigasyon özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="ce770-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Kurs.Bölüm](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="ce770-333">İskele Kursu modeli</span><span class="sxs-lookup"><span data-stu-id="ce770-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce770-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce770-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ce770-335">[İskele'deki](xref:data/ef-rp/intro#scaffold-the-student-model) talimatları uygulayın öğrenci modeli `Course` ve model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce770-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce770-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ce770-337">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ce770-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="ce770-338">Önceki komut `Course` modeli iskeleler.</span><span class="sxs-lookup"><span data-stu-id="ce770-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="ce770-339">Projeyi Visual Studio'da açın.</span><span class="sxs-lookup"><span data-stu-id="ce770-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="ce770-340">*Sayfaları/Dersleri/Index.cshtml.cs'yi* açın `OnGetAsync` ve yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ce770-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="ce770-341">İskele motoru `Department` navigasyon özelliği için istekli yükleme belirtti.</span><span class="sxs-lookup"><span data-stu-id="ce770-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="ce770-342">Yöntem, `Include` istekli yükleme yi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce770-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="ce770-343">Uygulamayı çalıştırın ve **Kurslar** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="ce770-344">Bölüm sütunu `DepartmentID`, hangi yararlı değildir görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="ce770-345">Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="ce770-346">Önceki kod ekler. `AsNoTracking`</span><span class="sxs-lookup"><span data-stu-id="ce770-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="ce770-347">`AsNoTracking`döndürülen varlıklar izlenmediği için performansı artırır.</span><span class="sxs-lookup"><span data-stu-id="ce770-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="ce770-348">Varlıklar, geçerli bağlamda güncelleştirilmeyecekleri için izlenmez.</span><span class="sxs-lookup"><span data-stu-id="ce770-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="ce770-349">*Sayfaları/Dersleri/Index.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="ce770-350">İskele kodunda aşağıdaki değişiklikler yapılmıştır:</span><span class="sxs-lookup"><span data-stu-id="ce770-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="ce770-351">Başlığı Index'ten Derslere değiştirdi.</span><span class="sxs-lookup"><span data-stu-id="ce770-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="ce770-352">Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID`</span><span class="sxs-lookup"><span data-stu-id="ce770-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="ce770-353">Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez.</span><span class="sxs-lookup"><span data-stu-id="ce770-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="ce770-354">Ancak, bu durumda birincil anahtar anlamlıdır.</span><span class="sxs-lookup"><span data-stu-id="ce770-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="ce770-355">Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="ce770-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="ce770-356">Kod, gezinti `Name` özelliğine `Department` yüklenen varlığın özelliğini `Department` görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ce770-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="ce770-357">Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="ce770-359">İlgili verileri Select ile yükleme</span><span class="sxs-lookup"><span data-stu-id="ce770-359">Loading related data with Select</span></span>

<span data-ttu-id="ce770-360">Yöntem, `OnGetAsync` ilgili verileri `Include` yöntemle yükler:</span><span class="sxs-lookup"><span data-stu-id="ce770-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ce770-361">Operatör `Select` yalnızca gerekli ilgili verileri yükler.</span><span class="sxs-lookup"><span data-stu-id="ce770-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="ce770-362">Tek öğeler için, `Department.Name` bir SQL INNER JOIN kullanır gibi.</span><span class="sxs-lookup"><span data-stu-id="ce770-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="ce770-363">Koleksiyonlar için başka bir veritabanı erişimi kullanır, ancak koleksiyonlar üzerindeki `Include` işleç de kullanır.</span><span class="sxs-lookup"><span data-stu-id="ce770-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="ce770-364">Aşağıdaki kod `Select` yöntemle ilgili verileri yükler:</span><span class="sxs-lookup"><span data-stu-id="ce770-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="ce770-365">Ne `CourseViewModel`var:</span><span class="sxs-lookup"><span data-stu-id="ce770-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="ce770-366">Tam bir örnek için [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) ve [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) bakın.</span><span class="sxs-lookup"><span data-stu-id="ce770-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="ce770-367">Kursları ve Kayıtları gösteren bir Eğitmen sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="ce770-368">Bu bölümde Eğitmenler sayfası oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="ce770-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="ce770-369">![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="ce770-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="ce770-370">Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ce770-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="ce770-371">Eğitmenlerlistesi `OfficeAssignment` varlıktan (Önceki resimdeki Office) ilgili verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="ce770-372">`OfficeAssignment` Varlıklar `Instructor` birden sıfıra veya bir ilişkisindedir.</span><span class="sxs-lookup"><span data-stu-id="ce770-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ce770-373">Araçlar için `OfficeAssignment` istekli yükleme kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="ce770-374">İlgili verilerin görüntülenmesi gerektiğinde istekli yükleme genellikle daha verimlidir.</span><span class="sxs-lookup"><span data-stu-id="ce770-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="ce770-375">Bu durumda, eğitmenler için ofis atamaları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="ce770-376">Kullanıcı bir eğitmen (önceki resimdeki Harui) seçtiğinde, ilgili `Course` varlıklar görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="ce770-377">Ve `Instructor` `Course` varlıklar çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ce770-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="ce770-378">Istekli yükleme `Course` varlıklar ve ilgili `Department` varlıklar için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="ce770-379">Bu durumda, yalnızca seçilen eğitmen için kurslar gerektiğinden, ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="ce770-380">Bu örnek, gezinti özellikleri olan varlıklarda gezinme özellikleri için istekli yüklemenin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="ce770-381">Kullanıcı bir ders seçtiğinde (önceki resimdeki kimya), `Enrollments` varlıktan ilgili veriler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="ce770-382">Bir önceki resimde öğrenci adı ve notu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="ce770-383">Ve `Course` `Enrollment` varlıklar bir-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="ce770-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="ce770-384">Eğitmen Dizini görünümü için bir görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="ce770-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="ce770-385">Eğitmenler sayfası üç farklı tablodan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="ce770-386">Üç tabloyu temsil eden üç varlığı içeren bir görünüm modeli oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ce770-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="ce770-387">*SchoolViewModels* klasöründe, aşağıdaki kodla *InstructorIndexData.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="ce770-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="ce770-388">İskele Eğitmen modeli</span><span class="sxs-lookup"><span data-stu-id="ce770-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce770-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce770-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ce770-390">[İskele'deki](xref:data/ef-rp/intro#scaffold-the-student-model) talimatları uygulayın öğrenci modeli `Instructor` ve model sınıfı için kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce770-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ce770-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ce770-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="ce770-392">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ce770-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="ce770-393">Önceki komut `Instructor` modeli iskeleler.</span><span class="sxs-lookup"><span data-stu-id="ce770-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="ce770-394">Uygulamayı çalıştırın ve eğitmenler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="ce770-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="ce770-395">*Sayfaları/Eğitmenleri/Index.cshtml.cs'yi* aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="ce770-396">Yöntem, `OnGetAsync` seçili eğitmenin kimliği için isteğe bağlı rota verilerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ce770-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="ce770-397">*Sayfalar/Eğitmenler/Index.cshtml.cs* dosyasındaki sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ce770-398">Sorgunun iki tane si vardır:</span><span class="sxs-lookup"><span data-stu-id="ce770-398">The query has two includes:</span></span>

* <span data-ttu-id="ce770-399">`OfficeAssignment`: Eğitmenler [görünümünde](#IP)görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="ce770-400">`CourseAssignments`: Bu da öğretilen dersleri getirir.</span><span class="sxs-lookup"><span data-stu-id="ce770-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="ce770-401">Eğitmenlerin Dizin sayfasını güncelleştirin</span><span class="sxs-lookup"><span data-stu-id="ce770-401">Update the instructors Index page</span></span>

<span data-ttu-id="ce770-402">*Sayfaları/Eğitmenleri/Index.cshtml'i* aşağıdaki biçimlendirmeyle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="ce770-403">Önceki biçimlendirme aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="ce770-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="ce770-404">Yönergeyi `page` `@page` `@page "{id:int?}"`' den .</span><span class="sxs-lookup"><span data-stu-id="ce770-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="ce770-405">`"{id:int?}"`bir rota şablonudur.</span><span class="sxs-lookup"><span data-stu-id="ce770-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="ce770-406">Rota şablonu, verileri yönlendirmek için URL'deki sonda sorgu dizelerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ce770-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="ce770-407">Örneğin, yalnızca yönergesi `@page` olan bir eğitmen için **Seç** bağlantısını tıklattığınızda aşağıdaki gibi bir URL üretir:</span><span class="sxs-lookup"><span data-stu-id="ce770-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="ce770-408">Sayfa yönergesi, `@page "{id:int?}"`önceki URL'dir:</span><span class="sxs-lookup"><span data-stu-id="ce770-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="ce770-409">Sayfa başlığı **Eğitmenler**olduğunu.</span><span class="sxs-lookup"><span data-stu-id="ce770-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="ce770-410">Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="ce770-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="ce770-411">Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment varlığı olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="ce770-412">Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="ce770-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="ce770-413">Bu jilet sözdizimi hakkında daha fazla şey için [Açık satır geçişine](xref:mvc/views/razor#explicit-line-transition) bakın.</span><span class="sxs-lookup"><span data-stu-id="ce770-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="ce770-414">Seçilen eğitmenin `class="success"` `tr` öğesine dinamik olarak eklenen kod eklendi.</span><span class="sxs-lookup"><span data-stu-id="ce770-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="ce770-415">Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="ce770-416">**Seç**etiketli yeni bir köprü eklendi.</span><span class="sxs-lookup"><span data-stu-id="ce770-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="ce770-417">Bu bağlantı, seçilen eğitmenin kimliğini `Index` yönteme gönderir ve bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="ce770-418">Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili `Location` `OfficeAssignment` varlığın (ofis) görüntüleniyor.</span><span class="sxs-lookup"><span data-stu-id="ce770-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="ce770-419">OfficeAssignment' null ise, boş bir tablo hücresi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="ce770-420">**Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ce770-420">Click on the **Select** link.</span></span> <span data-ttu-id="ce770-421">Satır stili değişir.</span><span class="sxs-lookup"><span data-stu-id="ce770-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="ce770-422">Seçilen eğitmen tarafından verilen dersleri ekleme</span><span class="sxs-lookup"><span data-stu-id="ce770-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="ce770-423">`OnGetAsync` *Sayfalar/Eğitmenler/Index.cs.cs'deki* yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="ce770-424">Ekle`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="ce770-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="ce770-425">Güncelleştirilmiş sorguyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="ce770-426">Önceki sorgu `Department` varlıkları ekler.</span><span class="sxs-lookup"><span data-stu-id="ce770-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="ce770-427">Bir eğitmen seçildiğinde aşağıdaki kod`id != null`çalıştırılır ( ).</span><span class="sxs-lookup"><span data-stu-id="ce770-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="ce770-428">Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="ce770-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="ce770-429">Görünüm modelinin `Courses` özelliği, o `Course` eğitmenin `CourseAssignments` gezinti özelliğinden gelen varlıklarla yüklenir.</span><span class="sxs-lookup"><span data-stu-id="ce770-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="ce770-430">Yöntem `Where` bir koleksiyon döndürür.</span><span class="sxs-lookup"><span data-stu-id="ce770-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="ce770-431">Önceki `Where` yöntemde, yalnızca tek `Instructor` bir varlık döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ce770-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="ce770-432">Yöntem, `Single` koleksiyonu tek `Instructor` bir varlığa dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ce770-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="ce770-433">`CourseAssignments` Varlık, `Instructor` özelliğe erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="ce770-434">`CourseAssignments`ilgili `Course` varlıklara erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Öğretim-Kurs m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="ce770-436">Yöntem, `Single` koleksiyonda yalnızca bir öğe olduğunda bir koleksiyonda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce770-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="ce770-437">Yöntem, `Single` koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ce770-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="ce770-438">Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir.</span><span class="sxs-lookup"><span data-stu-id="ce770-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="ce770-439">Boş `SingleOrDefault` bir koleksiyonda kullanma:</span><span class="sxs-lookup"><span data-stu-id="ce770-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="ce770-440">Bir özel durumla sonuçlanır (null reference'da bir `Courses` özellik bulmaya çalışmaktan).</span><span class="sxs-lookup"><span data-stu-id="ce770-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="ce770-441">Özel durum iletisi sorunun nedenini daha az açıkça gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="ce770-442">Bir kurs seçildiğinde aşağıdaki kod `Enrollments` görünüm modelinin özelliğini doldurur:</span><span class="sxs-lookup"><span data-stu-id="ce770-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="ce770-443">*Sayfalar/Eğitmenler/Index.cshtml* Jilet Sayfasının sonuna aşağıdaki işaretlemeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="ce770-444">Önceki biçimlendirme, bir eğitmen seçildiğinde bir eğitmenle ilgili derslerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="ce770-445">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="ce770-445">Test the app.</span></span> <span data-ttu-id="ce770-446">Eğitmenler sayfasındaki **Seç** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="ce770-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="ce770-447">Öğrenci verilerini gösterme</span><span class="sxs-lookup"><span data-stu-id="ce770-447">Show student data</span></span>

<span data-ttu-id="ce770-448">Bu bölümde, uygulama seçilen bir kursun öğrenci verilerini göstermek üzere güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="ce770-449">`OnGetAsync` *Sayfalar/Eğitmenler/Index.cs.cs* yöntemindeki sorguyu aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ce770-450">*Sayfaları/Eğitmenleri/Index.cshtml'i*güncelleştir.</span><span class="sxs-lookup"><span data-stu-id="ce770-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="ce770-451">Aşağıdaki biçimlendirmeyi dosyanın sonuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="ce770-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="ce770-452">Önceki biçimlendirme, seçilen kursa kayıtlı öğrencilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce770-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="ce770-453">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="ce770-454">Kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="ce770-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="ce770-456">Tek Kullanma</span><span class="sxs-lookup"><span data-stu-id="ce770-456">Using Single</span></span>

<span data-ttu-id="ce770-457">Yöntem, `Single` `Where` yöntemi ayrı `Where` ayrı çağırmak yerine koşulda geçebilir:</span><span class="sxs-lookup"><span data-stu-id="ce770-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="ce770-458">Önceki `Single` yaklaşım kullanarak `Where`üzerinde hiçbir fayda sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce770-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="ce770-459">Bazı geliştiriciler `Single` yaklaşım stilini tercih ediyor.</span><span class="sxs-lookup"><span data-stu-id="ce770-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="ce770-460">Açık yükleme</span><span class="sxs-lookup"><span data-stu-id="ce770-460">Explicit loading</span></span>

<span data-ttu-id="ce770-461">Geçerli kod için `Enrollments` istekli yükleme `Students`belirtir ve:</span><span class="sxs-lookup"><span data-stu-id="ce770-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="ce770-462">Kullanıcıların bir kurstaki kayıtları nadiren görmek istediklerini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="ce770-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="ce770-463">Bu durumda, en iyi duruma getirilmesi yalnızca istenirse kayıt verilerini yüklemek olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ce770-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="ce770-464">Bu bölümde, `OnGetAsync` açık yükleme `Enrollments` ve kullanmak `Students`için güncellenir .</span><span class="sxs-lookup"><span data-stu-id="ce770-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="ce770-465">Aşağıdaki `OnGetAsync` kodu ile güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="ce770-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="ce770-466">Önceki kod, Kayıt ve öğrenci verileri için *ThenInclude* yöntemini düşürür.</span><span class="sxs-lookup"><span data-stu-id="ce770-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="ce770-467">Bir kurs seçilirse, vurgulanan kod alır:</span><span class="sxs-lookup"><span data-stu-id="ce770-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="ce770-468">`Enrollment` Seçilen kursun varlıkları.</span><span class="sxs-lookup"><span data-stu-id="ce770-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="ce770-469">Her `Student` biri `Enrollment`için varlıklar .</span><span class="sxs-lookup"><span data-stu-id="ce770-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="ce770-470">Önceki kod açıklamalarının `.AsNoTracking()`dışına çıktığına dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="ce770-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="ce770-471">Gezinti özellikleri yalnızca izlenen varlıklar için açıkça yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="ce770-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="ce770-472">Uygulamayı test etme.</span><span class="sxs-lookup"><span data-stu-id="ce770-472">Test the app.</span></span> <span data-ttu-id="ce770-473">Kullanıcı açısından bakıldığında, uygulama önceki sürümle aynı şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="ce770-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="ce770-474">Sonraki öğretici, ilgili verilerin nasıl güncelleştirilebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce770-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce770-475">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ce770-475">Additional resources</span></span>

* [<span data-ttu-id="ce770-476">Bu öğreticinin YouTube sürümü (part1)</span><span class="sxs-lookup"><span data-stu-id="ce770-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="ce770-477">Bu öğretici Nin YouTube sürümü (part2)</span><span class="sxs-lookup"><span data-stu-id="ce770-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="ce770-478">[Önceki](xref:data/ef-rp/complex-data-model)
>[Sonraki](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="ce770-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
