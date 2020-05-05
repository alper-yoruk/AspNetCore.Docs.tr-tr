---
title: 'Öğretici: EF Core ile ilgili verileri okuma-ASP.NET MVC'
description: Bu öğreticide ilgili verileri okuyabilir ve görüntüleyebilirsiniz. Bu, Entity Framework, gezinti özelliklerine yüklediği verileri okur.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 7cc400218d7ad8717311e56ec5b3fc190857862c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773542"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="88538-103">Öğretici: EF Core ile ilgili verileri okuma-ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="88538-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="88538-104">Önceki öğreticide, okul veri modelini tamamladınız.</span><span class="sxs-lookup"><span data-stu-id="88538-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="88538-105">Bu öğreticide ilgili verileri okur ve görüntüleriz. Bu, Entity Framework, gezinti özelliklerine yüklediği veriler.</span><span class="sxs-lookup"><span data-stu-id="88538-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="88538-106">Aşağıdaki çizimlerde, birlikte çalışacağımız sayfalar gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="88538-106">The following illustrations show the pages that you'll work with.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

<span data-ttu-id="88538-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="88538-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="88538-110">İlgili verileri yüklemeyi öğrenin</span><span class="sxs-lookup"><span data-stu-id="88538-110">Learn how to load related data</span></span>
> * <span data-ttu-id="88538-111">Kurslar sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-111">Create a Courses page</span></span>
> * <span data-ttu-id="88538-112">Eğitmenler sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-112">Create an Instructors page</span></span>
> * <span data-ttu-id="88538-113">Açık yükleme hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="88538-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="88538-114">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="88538-114">Prerequisites</span></span>

* [<span data-ttu-id="88538-115">Karmaşık veri modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="88538-116">İlgili verileri yüklemeyi öğrenin</span><span class="sxs-lookup"><span data-stu-id="88538-116">Learn how to load related data</span></span>

<span data-ttu-id="88538-117">Entity Framework gibi nesne Ilişkisel eşleme (ORM) yazılımının bir varlığın gezinti özelliklerine ilgili verileri yükleyebilmesinin birkaç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="88538-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="88538-118">Eager yükleniyor.</span><span class="sxs-lookup"><span data-stu-id="88538-118">Eager loading.</span></span> <span data-ttu-id="88538-119">Varlık okurken ilgili veriler onunla birlikte alınır.</span><span class="sxs-lookup"><span data-stu-id="88538-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="88538-120">Bu, genellikle gereken tüm verileri alan tek bir JOIN sorgusuna neden olur.</span><span class="sxs-lookup"><span data-stu-id="88538-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="88538-121">`Include` Ve `ThenInclude` yöntemlerini kullanarak Entity Framework Core ' de bir Eager yüklemesi belirlersiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="88538-123">Bazı verileri ayrı sorgularda alabilir ve "düzeltmeler" i "düzeltir" seçeneğini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="88538-124">Diğer bir deyişle, EF, daha önce alınan varlıkların gezinti özelliklerine ait oldukları ayrı alınan varlıkları otomatik olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="88538-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="88538-125">İlgili verileri alan sorgu için, `Load` `ToList` veya `Single`gibi bir liste veya nesne döndüren bir yöntem yerine yöntemini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="88538-127">Açık yükleme.</span><span class="sxs-lookup"><span data-stu-id="88538-127">Explicit loading.</span></span> <span data-ttu-id="88538-128">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="88538-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="88538-129">Gerekirse ilgili verileri alan kodu yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="88538-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="88538-130">Ayrı sorgularla yükleme durumunda olduğu gibi, açıkça yükleme, veritabanına gönderilen birden çok sorgu ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="88538-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="88538-131">Fark, açık yükleme ile kod, yüklenecek gezinti özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="88538-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="88538-132">Entity Framework Core 1,1 ' de, `Load` açık yükleme yapmak için yöntemini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="88538-133">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88538-133">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="88538-135">Yavaş yükleme.</span><span class="sxs-lookup"><span data-stu-id="88538-135">Lazy loading.</span></span> <span data-ttu-id="88538-136">Varlık ilk kez okunmadıysa ilgili veriler alınmadı.</span><span class="sxs-lookup"><span data-stu-id="88538-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="88538-137">Ancak, bir gezinti özelliğine ilk kez erişmeye çalıştığınızda, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="88538-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="88538-138">Bir gezinti özelliğinden ilk kez veri almaya çalıştığınızda veritabanına bir sorgu gönderilir.</span><span class="sxs-lookup"><span data-stu-id="88538-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="88538-139">Entity Framework Core 1,0, yavaş yüklemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="88538-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="88538-140">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="88538-140">Performance considerations</span></span>

<span data-ttu-id="88538-141">Alınan her varlık için ilgili verilerin gerekli olduğunu biliyorsanız, tek bir sorgu genellikle en iyi performansı sunar, çünkü veritabanına gönderilen tek bir sorgu genellikle alınan her varlık için ayrı sorgulardan daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="88538-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="88538-142">Örneğin, her departmanın on ile ilgili kurs olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="88538-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="88538-143">Tüm ilgili verilerin bir şekilde yüklenmesi, tek bir (JOIN) sorgusuna ve veritabanına yönelik tek gidiş dönüş oluşmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="88538-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="88538-144">Her bölüme yönelik kurslar için ayrı bir sorgu, veritabanı üzerinde on bir gidiş dönüş oluşmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="88538-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="88538-145">Gecikme süresi yüksek olduğunda veritabanına yönelik ek gidiş dönüşler özellikle performansa neden olur.</span><span class="sxs-lookup"><span data-stu-id="88538-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="88538-146">Öte yandan, bazı senaryolarda ayrı sorgular daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="88538-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="88538-147">Tek bir sorgudaki ilgili verilerin tümünü yükleme, SQL Server verimli bir şekilde bir birleştirmenin oluşturulmasına neden olabilir ve bu da etkili bir şekilde işleyemez.</span><span class="sxs-lookup"><span data-stu-id="88538-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="88538-148">Ya da bir varlığın gezinti özelliklerine yalnızca işlemekte olduğunuz varlıkların kümesinin bir alt kümesi için erişmeniz gerekiyorsa, her şeyin en baştan yüklenmesi gerekenden fazla veri alacağından ayrı sorgular daha iyi çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="88538-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="88538-149">Performans önemliyse, en iyi seçimi yapmak için her iki şekilde de performansı test etmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="88538-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="88538-150">Kurslar sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-150">Create a Courses page</span></span>

<span data-ttu-id="88538-151">Kurs varlığı, kursun atandığı departmanın departman varlığını içeren bir gezinti özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="88538-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="88538-152">Bir kurs listesinde atanan departmanın adını göstermek için, `Course.Department` Gezinti özelliğindeki departman varlığındaki Name özelliğini almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="88538-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="88538-153">Aşağıdaki çizimde gösterildiği gibi, daha önce öğrenciler denetleyicisi için yaptığınız Entity Framework desteği ' ı kullanarak, **MVC denetleyicisi için, görünümler ile** aynı seçenekleri kullanarak kurs varlık türü için coursescontroller adlı bir denetleyici oluşturun:</span><span class="sxs-lookup"><span data-stu-id="88538-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Kurs denetleyicisi ekleme](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="88538-155">*CoursesController.cs* açın ve `Index` metodunu inceleyin.</span><span class="sxs-lookup"><span data-stu-id="88538-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="88538-156">Otomatik yapı iskelesi, `Department` `Include` yöntemi kullanılarak gezinti özelliği için bir Eager yüklemesi belirtti.</span><span class="sxs-lookup"><span data-stu-id="88538-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="88538-157">`Index` Yöntemini `IQueryable` , kurs varlıklarını döndüren ' için daha uygun bir ad kullanan aşağıdaki kodla değiştirin (`courses` yerine `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="88538-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="88538-158">*Views/kurslar/Index. cshtml* dosyasını açın ve şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="88538-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="88538-159">Değişiklikler vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="88538-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="88538-160">Yapı iskelesi kodunda aşağıdaki değişiklikleri yaptınız:</span><span class="sxs-lookup"><span data-stu-id="88538-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="88538-161">Başlık dizinden kurslar olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="88538-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="88538-162">Özellik değerini gösteren bir sayı sütunu eklendi. **Number** `CourseID`</span><span class="sxs-lookup"><span data-stu-id="88538-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="88538-163">Birincil anahtarlar, genellikle son kullanıcılara anlamlı olduklarından, varsayılan olarak yapı iskelesi göstermemektedir.</span><span class="sxs-lookup"><span data-stu-id="88538-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="88538-164">Ancak, bu durumda birincil anahtar anlamlı olur ve göstermek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="88538-165">Departman adını göstermek için **Departman** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="88538-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="88538-166">Kod, `Department` gezinti özelliğine `Name` yüklenen departman varlığının özelliğini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="88538-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="88538-167">Uygulamayı çalıştırın ve bölüm adlarıyla listeyi görmek için **Kurslar** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88538-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Dizin sayfası](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="88538-169">Eğitmenler sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-169">Create an Instructors page</span></span>

<span data-ttu-id="88538-170">Bu bölümde, Eğitmenler sayfasını görüntülemek için eğitmen varlığı için bir denetleyici ve görünüm oluşturacaksınız:</span><span class="sxs-lookup"><span data-stu-id="88538-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Eğitmenler Dizin sayfası](read-related-data/_static/instructors-index.png)

<span data-ttu-id="88538-172">Bu sayfa aşağıdaki yollarla ilgili verileri okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="88538-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="88538-173">Eğitmenler listesi, OfficeAssignment varlığındaki ilgili verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="88538-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="88538-174">Eğitmen ve OfficeAssignment varlıkları bire sıfır veya-bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="88538-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="88538-175">OfficeAssignment varlıkları için Eager yükleme kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="88538-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="88538-176">Daha önce açıklandığı gibi, birincil tablonun alınan tüm satırları için ilgili verilere ihtiyacınız olduğunda Eager yüklemesi genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="88538-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="88538-177">Bu durumda, tüm görüntülenen Eğitmenler için Office atamalarını göstermek istersiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="88538-178">Kullanıcı bir eğitmen seçtiğinde ilgili kurs varlıkları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="88538-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="88538-179">Eğitmen ve kurs varlıkları çoktan çoğa bir ilişkidir.</span><span class="sxs-lookup"><span data-stu-id="88538-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="88538-180">Kurs varlıkları ve ilgili departman varlıkları için Eager yükleme kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="88538-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="88538-181">Bu durumda, yalnızca seçili eğitmen için kurslar gerektiğinden ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="88538-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="88538-182">Ancak, bu örnek, gezinti özellikleri ' nde olan varlıkların içindeki gezinti özellikleri için Eager yükleme 'nin nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="88538-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="88538-183">Kullanıcı bir kurs seçtiğinde, kayıt varlığı kümesindeki ilgili veriler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="88538-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="88538-184">Kurs ve kayıt varlıkları bire çok ilişkisinde.</span><span class="sxs-lookup"><span data-stu-id="88538-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="88538-185">Kayıt varlıkları ve ilgili öğrenci varlıkları için ayrı sorgular kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="88538-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="88538-186">Eğitmen dizini görünümü için bir görünüm modeli oluşturun</span><span class="sxs-lookup"><span data-stu-id="88538-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="88538-187">Eğitmenler sayfasında, üç farklı tablodan alınan veriler gösterilir.</span><span class="sxs-lookup"><span data-stu-id="88538-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="88538-188">Bu nedenle, her biri tablolardan birine ait verileri tutan üç özellik içeren bir görünüm modeli oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="88538-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="88538-189">*SchoolViewModels* klasöründe *InstructorIndexData.cs* oluşturun ve mevcut kodu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="88538-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="88538-190">Eğitmen denetleyicisi ve görünümleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="88538-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="88538-191">Aşağıdaki çizimde gösterildiği gibi EF okuma/yazma eylemleri ile bir eğitmenler denetleyicisi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="88538-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Eğitmenler denetleyicisi Ekle](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="88538-193">*InstructorsController.cs* açın ve ViewModel ad alanı için bir using ifadesini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="88538-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="88538-194">İlişkili verilerin yüklenmesini ve görünüm modeline koymak için Dizin yöntemini aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="88538-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="88538-195">Yöntemi, seçilen eğitmenin ve seçili kursun`id`kimlik değerlerini sağlayan isteğe bağlı yol verilerini`courseID`() ve bir sorgu dizesi parametresini () kabul eder.</span><span class="sxs-lookup"><span data-stu-id="88538-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="88538-196">Parametreler, sayfadaki Köprü **seçme** ile sağlanır.</span><span class="sxs-lookup"><span data-stu-id="88538-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="88538-197">Kod, görünüm modelinin bir örneğini oluşturarak ve bu örneğe eğitmen listesine yerleştirilerek başlar.</span><span class="sxs-lookup"><span data-stu-id="88538-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="88538-198">Kod, `Instructor.OfficeAssignment` ve `Instructor.CourseAssignments` gezinti özellikleri için Eager yüklemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="88538-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="88538-199">`CourseAssignments` Özelliği içinde, `Course` özelliği yüklenir, ve içinde, `Enrollments` ve `Department` özellikleri yüklenir ve her `Enrollment` bir varlıkta `Student` özelliği yüklenir.</span><span class="sxs-lookup"><span data-stu-id="88538-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="88538-200">Görünüm her zaman OfficeAssignment varlığını gerektirdiğinden, bunu aynı sorguda getirmek daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="88538-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="88538-201">Web sayfasında bir eğitmen seçildiğinde kurs varlıkları gereklidir, bu yüzden tek bir sorgu birden çok sorgudan daha iyidir, bu nedenle yalnızca sayfa, hiçbir zaman tarafından seçilen bir kurs ile daha sık görüntüleniyorsa.</span><span class="sxs-lookup"><span data-stu-id="88538-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="88538-202">Kodu yinelenir `CourseAssignments` ve ' `Course` den `Course`iki özellik olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88538-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="88538-203">İlk `ThenInclude` çağrı dizesi, `CourseAssignment.Course` `Course.Enrollments`ve `Enrollment.Student`' ı alır.</span><span class="sxs-lookup"><span data-stu-id="88538-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="88538-204">Bu noktada, başka bir deyişle, ihtiyacınız `ThenInclude` olmayan, ' nin `Student`gezinti özellikleri için bir tane olur.</span><span class="sxs-lookup"><span data-stu-id="88538-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="88538-205">Ancak çağıran `Include` özellikleri ile `Instructor` başlar, bu kez `Course.Department` zinciri yeniden gitmeniz gerekir. `Course.Enrollments`</span><span class="sxs-lookup"><span data-stu-id="88538-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="88538-206">Aşağıdaki kod, bir eğitmen seçildiğinde yürütülür.</span><span class="sxs-lookup"><span data-stu-id="88538-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="88538-207">Seçilen eğitmen, görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="88538-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="88538-208">Görünüm modelinin `Courses` özelliği daha sonra bu eğitmenin `CourseAssignments` gezinti özelliğinden kurs varlıklarıyla birlikte yüklenir.</span><span class="sxs-lookup"><span data-stu-id="88538-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="88538-209">`Where` Yöntemi bir koleksiyon döndürür, ancak bu yönteme geçirilen kriterler yalnızca tek bir eğitmen varlığının döndürüldüğünden sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="88538-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="88538-210">`Single` Yöntemi, koleksiyonu tek bir eğitmen varlığına dönüştürür, bu da o varlığın `CourseAssignments` özelliğine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="88538-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="88538-211">`CourseAssignments` Özelliği, yalnızca `CourseAssignment` ilgili `Course` varlıkları istediğiniz varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="88538-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="88538-212">Koleksiyonda yalnızca bir `Single` öğe olacağını bildiğiniz durumlarda yöntemini kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="88538-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="88538-213">Tek yöntem, koleksiyon boş veya birden fazla öğe varsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="88538-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="88538-214">Koleksiyon boşsa varsayılan `SingleOrDefault`bir değer (Bu durumda null) döndüren alternatif bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="88538-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="88538-215">Bununla birlikte, bu durumda yine de bir özel durum oluşmasına neden olur (null başvuru üzerinde bir `Courses` Özellik bulmaya çalışırken) ve özel durum iletisi sorunun nedenini daha az göstermez.</span><span class="sxs-lookup"><span data-stu-id="88538-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="88538-216">`Single` Yöntemini çağırdığınızda, `Where` yöntemi ayrı olarak çağırmak yerine WHERE koşulunu da geçirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="88538-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="88538-217">Onun yerine:</span><span class="sxs-lookup"><span data-stu-id="88538-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="88538-218">Ardından, bir kurs seçilmişse, seçilen kurs, görünüm modelindeki kurslar listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="88538-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="88538-219">Ardından, görünüm modelinin `Enrollments` özelliği, bu kursun `Enrollments` gezinti özelliğinden kayıt varlıklarıyla birlikte yüklenir.</span><span class="sxs-lookup"><span data-stu-id="88538-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="88538-220">Eğitmen dizini görünümünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="88538-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="88538-221">*Views/eğitmenler/Index. cshtml*içinde, şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="88538-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="88538-222">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="88538-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="88538-223">Varolan koda aşağıdaki değişiklikleri yaptınız:</span><span class="sxs-lookup"><span data-stu-id="88538-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="88538-224">Model sınıfı olarak `InstructorIndexData`değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="88538-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="88538-225">Sayfa başlığı **dizinden** **eğitmenler**olarak değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="88538-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="88538-226">Yalnızca null olmaması halinde `item.OfficeAssignment` görüntülenen bir Office sütunu eklendi. **Office** `item.OfficeAssignment.Location`</span><span class="sxs-lookup"><span data-stu-id="88538-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="88538-227">(Bu bire sıfır veya-bir ilişki olduğundan ilgili bir OfficeAssignment varlığı bulunmayabilir.)</span><span class="sxs-lookup"><span data-stu-id="88538-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="88538-228">Her bir eğitmen tarafından taders kurslarını görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="88538-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="88538-229">Daha fazla bilgi için, Razor söz dizimi makalesindeki [açık satır geçişi](xref:mvc/views/razor#explicit-line-transition) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="88538-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="88538-230">Seçilen eğitmenin `tr` öğesine dinamik olarak `class="success"` ekleyen kod eklendi.</span><span class="sxs-lookup"><span data-stu-id="88538-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="88538-231">Bu, bir önyükleme sınıfı kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="88538-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="88538-232">Her satırdaki diğer bağlantılardan hemen önce **Seç** etiketli yeni bir köprü eklendiğinde, SEÇILEN eğitmenin kimliğinin `Index` yönteme gönderilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="88538-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="88538-233">Uygulamayı çalıştırın ve **eğitmenler** sekmesini seçin. Sayfa ilgili OfficeAssignment varlıklarının Location özelliğini ve ilişkili OfficeAssignment varlığı olmadığında boş bir tablo hücresini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="88538-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Eğitmenler Dizin sayfası seçili öğe yok](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="88538-235">*Views/eğitmenler/Index. cshtml* dosyasında, Kapanış tablosu öğesinden sonra (dosyanın sonunda) aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="88538-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="88538-236">Bu kod, bir eğitmen seçildiğinde bir eğitmenin ilgili kursların bir listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="88538-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="88538-237">Bu kod, `Courses` kurs listesini görüntülemek için görünüm modelinin özelliğini okur.</span><span class="sxs-lookup"><span data-stu-id="88538-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="88538-238">Ayrıca, `Index` SEÇILI kursun kimliğini eylem yöntemine gönderen bir **seçme** Köprüsü de sağlar.</span><span class="sxs-lookup"><span data-stu-id="88538-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="88538-239">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="88538-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="88538-240">Artık Seçili eğitmenin atandığı kursları görüntüleyen bir kılavuz görürsünüz ve her kurs için atanan departmanın adını görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="88538-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Eğitmenler Dizin sayfası eğitmeni seçildi](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="88538-242">Yeni eklediğiniz kod bloğundan sonra aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="88538-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="88538-243">Bu, kurs seçildiğinde bir kursa kaydedilen öğrencilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="88538-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="88538-244">Bu kod, kursa kayıtlı öğrencilerin listesini görüntülemek için görünüm modelinin kayıtları özelliğini okur.</span><span class="sxs-lookup"><span data-stu-id="88538-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="88538-245">Sayfayı yeniden yenileyip bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="88538-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="88538-246">Ardından, kayıtlı öğrenciler ve bunların onların listesini görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="88538-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfası eğitmeni ve kursu seçildi](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="88538-248">Açık yükleme hakkında</span><span class="sxs-lookup"><span data-stu-id="88538-248">About explicit loading</span></span>

<span data-ttu-id="88538-249">*InstructorsController.cs*' de eğitmenler listesini aldığınızda, `CourseAssignments` gezinti özelliği için bir Eager yüklemesi belirttiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="88538-250">Kullanıcılardan yalnızca, seçili bir eğitmenin ve kursla kayıtlarını yalnızca nadiren görmek istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="88538-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="88538-251">Bu durumda, kayıt verilerini yalnızca istenirse yüklemek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="88538-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="88538-252">Açık yüklemenin nasıl yapılacağını gösteren bir örnek görmek için, `Index` yöntemini aşağıdaki kodla değiştirin, bu da kayıtları için Eager yüklemeyi kaldırır ve bu özelliği açıkça yükler.</span><span class="sxs-lookup"><span data-stu-id="88538-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="88538-253">Kod değişiklikleri vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="88538-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="88538-254">Yeni kod, eğitmen varlıklarını alan koddan kayıt verileri için *Thenınclude* Yöntem çağrılarını bırakır.</span><span class="sxs-lookup"><span data-stu-id="88538-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="88538-255">Ayrıca düşün `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="88538-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="88538-256">Bir eğitmen ve kurs seçilirse, vurgulanan kod seçili kurs için kayıt varlıklarını ve her kayıt için öğrenci varlıklarını alır.</span><span class="sxs-lookup"><span data-stu-id="88538-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="88538-257">Uygulamayı çalıştırın, şimdi eğitmenler dizin sayfasına gidin ve sayfada görüntülendikleriyle ilgili hiçbir fark görmezsiniz; ancak verilerin nasıl alındığını değiştirmiş olursunuz.</span><span class="sxs-lookup"><span data-stu-id="88538-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="88538-258">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="88538-258">Get the code</span></span>

[<span data-ttu-id="88538-259">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="88538-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="88538-260">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="88538-260">Next steps</span></span>

<span data-ttu-id="88538-261">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="88538-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="88538-262">İlgili verileri yükleme hakkında öğrenilen</span><span class="sxs-lookup"><span data-stu-id="88538-262">Learned how to load related data</span></span>
> * <span data-ttu-id="88538-263">Bir kurslar sayfası oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="88538-263">Created a Courses page</span></span>
> * <span data-ttu-id="88538-264">Bir eğitmenler sayfası oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="88538-264">Created an Instructors page</span></span>
> * <span data-ttu-id="88538-265">Açık yükleme hakkında bilgi edinildi</span><span class="sxs-lookup"><span data-stu-id="88538-265">Learned about explicit loading</span></span>

<span data-ttu-id="88538-266">İlgili verileri güncelleştirme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="88538-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="88538-267">İlgili verileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="88538-267">Update related data</span></span>](update-related-data.md)
