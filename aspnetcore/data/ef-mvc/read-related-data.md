---
title: 'Öğretici: İlgili verileri okuyun - EF Core ile ASP.NET MVC'
description: Bu eğitimde, ilgili verileri , yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri okuyup görüntüleceksiniz.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: a6e63723101ab09219db81ee9796c3938a612226
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657110"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="0f179-103">Öğretici: İlgili verileri okuyun - EF Core ile ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="0f179-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="0f179-104">Önceki eğitimde, Okul veri modelini tamamladınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="0f179-105">Bu eğitimde, ilgili verileri okuyacak ve görüntüleceksiniz -- yani Varlık Çerçevesi'nin gezinti özelliklerine yüklediğiniz verileri.</span><span class="sxs-lookup"><span data-stu-id="0f179-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="0f179-106">Aşağıdaki çizimler, birlikte çalışacağınız sayfaları gösterir.</span><span class="sxs-lookup"><span data-stu-id="0f179-106">The following illustrations show the pages that you'll work with.</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

<span data-ttu-id="0f179-109">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0f179-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0f179-110">İlgili verileri nasıl yükleyeceklerini öğrenin</span><span class="sxs-lookup"><span data-stu-id="0f179-110">Learn how to load related data</span></span>
> * <span data-ttu-id="0f179-111">Kurslar sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-111">Create a Courses page</span></span>
> * <span data-ttu-id="0f179-112">Eğitmenler sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-112">Create an Instructors page</span></span>
> * <span data-ttu-id="0f179-113">Açık yükleme hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="0f179-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0f179-114">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0f179-114">Prerequisites</span></span>

* [<span data-ttu-id="0f179-115">Karmaşık veri modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="0f179-116">İlgili verileri nasıl yükleyeceklerini öğrenin</span><span class="sxs-lookup"><span data-stu-id="0f179-116">Learn how to load related data</span></span>

<span data-ttu-id="0f179-117">Entity Framework gibi Nesne İlişkisel Eşleme (ORM) yazılımının ilgili verileri bir varlığın gezinti özelliklerine yükleyebildiği birkaç yöntem vardır:</span><span class="sxs-lookup"><span data-stu-id="0f179-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="0f179-118">Hevesli yükleme.</span><span class="sxs-lookup"><span data-stu-id="0f179-118">Eager loading.</span></span> <span data-ttu-id="0f179-119">Varlık okunduğunda, ilgili veriler onunla birlikte alınır.</span><span class="sxs-lookup"><span data-stu-id="0f179-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="0f179-120">Bu genellikle, gereken tüm verileri alan tek bir birleştirme sorgusuyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="0f179-121">Taraf Framework Core'da istekli `Include` yüklemeyi `ThenInclude` ve yöntemleri kullanarak belirtirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0f179-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Eager yükleme örneği](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="0f179-123">Bazı verileri ayrı sorgularda alabilirsiniz ve EF gezinti özelliklerini "düzeltir".</span><span class="sxs-lookup"><span data-stu-id="0f179-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="0f179-124">Diğer bir deyişle, EF, daha önce alınan varlıkların gezinti özelliklerine ait oldukları ayrı olarak alınan varlıkları otomatik olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="0f179-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="0f179-125">İlgili verileri alan sorgu için, bir `Load` liste veya nesne döndüren bir yöntem `ToList` yerine `Single`yöntemi kullanabilirsiniz, örneğin veya .</span><span class="sxs-lookup"><span data-stu-id="0f179-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Ayrı sorgular örneği](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="0f179-127">Açık yükleme.</span><span class="sxs-lookup"><span data-stu-id="0f179-127">Explicit loading.</span></span> <span data-ttu-id="0f179-128">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="0f179-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="0f179-129">Gerektiğinde ilgili verileri alan kod yazarsınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="0f179-130">Ayrı sorgularla istekli yükleme durumunda olduğu gibi, açık yükleme veritabanına gönderilen birden çok sorguyla sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="0f179-131">Fark, açık yükleme ile, kod yüklenecek navigasyon özelliklerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0f179-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="0f179-132">Varlık Framework Core 1.1'de `Load` açık yükleme yapmak için yöntemi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0f179-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="0f179-133">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0f179-133">For example:</span></span>

  ![Açık yükleme örneği](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="0f179-135">Tembel yükleme.</span><span class="sxs-lookup"><span data-stu-id="0f179-135">Lazy loading.</span></span> <span data-ttu-id="0f179-136">Varlık ilk okunduğunda, ilgili veriler alınmaz.</span><span class="sxs-lookup"><span data-stu-id="0f179-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="0f179-137">Ancak, bir gezinti özelliğine ilk kez erişmeye çalıştığınızda, bu gezinti özelliği için gereken veriler otomatik olarak alınır.</span><span class="sxs-lookup"><span data-stu-id="0f179-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="0f179-138">Bir gezinti özelliğinden ilk kez veri almaya çalıştığınızda veritabanına bir sorgu gönderilir.</span><span class="sxs-lookup"><span data-stu-id="0f179-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="0f179-139">Entity Framework Core 1.0 tembel yüklemeyi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="0f179-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="0f179-140">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="0f179-140">Performance considerations</span></span>

<span data-ttu-id="0f179-141">Alınan her varlık için ilgili verilere ihtiyacınız olduğunu biliyorsanız, veritabanına gönderilen tek bir sorgu alınan her varlık için ayrı sorgulardan genellikle daha verimli olduğundan, hızlı yükleme genellikle en iyi performansı sunar.</span><span class="sxs-lookup"><span data-stu-id="0f179-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="0f179-142">Örneğin, her bölümün ilgili on dersi olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="0f179-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="0f179-143">İlgili tüm verilerin hevesle yüklenmesi, yalnızca tek bir (birleştirme) sorgusu na ve veritabanına tek bir gidiş-dönüş seyahatle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="0f179-144">Her bölüm için kurslar için ayrı bir sorgu veritabanına on bir gidiş-dönüş geziler neden olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0f179-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="0f179-145">Veritabanına yapılan ekstra gidiş-dönüş yolculuklar, gecikme oranı yüksek olduğunda performansa özellikle zarar verir.</span><span class="sxs-lookup"><span data-stu-id="0f179-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="0f179-146">Diğer taraftan, bazı senaryolarda ayrı sorgular daha verimlidir.</span><span class="sxs-lookup"><span data-stu-id="0f179-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="0f179-147">İlgili tüm verilerin tek bir sorguda hevesle yüklenmesi, SQL Server'ın verimli bir şekilde işlenemeyebileceği çok karmaşık bir birleştirme oluşturulmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="0f179-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="0f179-148">Veya yalnızca işlediğiniz varlıkların kümesinin bir alt kümesi için bir varlığın gezinti özelliklerine erişmeniz gerekiyorsa, her şeyin önceden yüklenilen istekli yüklenmesi ihtiyacınız olandan daha fazla veri alacağı için ayrı sorgular daha iyi performans gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="0f179-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="0f179-149">Performans kritikse, en iyi seçimi yapmak için performansı her iki şekilde de test etmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="0f179-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="0f179-150">Kurslar sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-150">Create a Courses page</span></span>

<span data-ttu-id="0f179-151">Kurs tüzel kişiliği, kursun atandığı bölümün Bölüm tüzel kişiliğini içeren bir navigasyon özelliği içerir.</span><span class="sxs-lookup"><span data-stu-id="0f179-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="0f179-152">Atanan bölümün adını bir ders listesinde görüntülemek için, gezinti özelliğinde bulunan Bölüm kuruluşundan Ad `Course.Department` özelliğini almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0f179-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="0f179-153">Aşağıdaki resimde gösterildiği gibi, Daha önce Öğrenci denetleyicisi için yaptığınız Entity Framework iskelesini **kullanarak, mvc denetleyicisi** için görünümlere sahip aynı seçenekleri kullanarak, Ders varlık türü için CoursesController adında bir denetleyici oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0f179-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Ders Ekle denetleyicisi](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="0f179-155">*CoursesController.cs* açın ve `Index` yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0f179-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="0f179-156">Otomatik iskele `Department` `Include` yöntemini kullanarak navigasyon özelliği için istekli yükleme belirtmiştir.</span><span class="sxs-lookup"><span data-stu-id="0f179-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="0f179-157">Yöntemi, Ders varlıklarını döndüren varlıklar `IQueryable` için daha uygun bir`courses` ad `schoolContext`kullanan aşağıdaki kodla değiştirin ( bunun yerine): `Index`</span><span class="sxs-lookup"><span data-stu-id="0f179-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="0f179-158">*Görünümler/Kurslar/Index.cshtml'i* açın ve şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0f179-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="0f179-159">Değişiklikler vurgulanır:</span><span class="sxs-lookup"><span data-stu-id="0f179-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="0f179-160">İskele kodunda aşağıdaki değişiklikleri yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0f179-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="0f179-161">Başlığı Index'ten Derslere değiştirdi.</span><span class="sxs-lookup"><span data-stu-id="0f179-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="0f179-162">Özellik değerini gösteren bir Sayı sütunu eklendi. **Number** `CourseID`</span><span class="sxs-lookup"><span data-stu-id="0f179-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="0f179-163">Varsayılan olarak, birincil anahtarlar genellikle son kullanıcılar için anlamsız olduğundan İskele yekpişmez.</span><span class="sxs-lookup"><span data-stu-id="0f179-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="0f179-164">Ancak, bu durumda birincil anahtar anlamlıdır ve bunu göstermek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="0f179-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="0f179-165">Bölüm adını görüntülemek için **Bölüm** sütunu değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="0f179-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="0f179-166">Kod, gezinti `Name` özelliğine yüklenen Bölüm kuruluşunun `Department` özelliğini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0f179-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="0f179-167">Uygulamayı çalıştırın ve bölüm adlarının yer alan listesini görmek için **Dersler** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0f179-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurslar Endeksi sayfası](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="0f179-169">Eğitmenler sayfası oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-169">Create an Instructors page</span></span>

<span data-ttu-id="0f179-170">Bu bölümde, Eğitmenler sayfasını görüntülemek için Eğitmen varlığı için bir denetleyici ve görünüm oluşturacaksınız:</span><span class="sxs-lookup"><span data-stu-id="0f179-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Eğitmenler Endeksi sayfası](read-related-data/_static/instructors-index.png)

<span data-ttu-id="0f179-172">Bu sayfa, ilgili verileri aşağıdaki şekillerde okur ve görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0f179-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="0f179-173">Eğitmenler listesi OfficeAssignment kuruluşundan ilgili verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0f179-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="0f179-174">Eğitmen ve OfficeAtama varlıkları birden sıfıra veya bir ilişkisindedir.</span><span class="sxs-lookup"><span data-stu-id="0f179-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="0f179-175">OfficeAssignment varlıkları için istekli yükleme kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="0f179-176">Daha önce açıklandığı gibi, birincil tablonun tüm alınan satırları için ilgili verilere ihtiyaç duyduğunuzda, istekli yükleme genellikle daha verimlidir.</span><span class="sxs-lookup"><span data-stu-id="0f179-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="0f179-177">Bu durumda, görüntülenen tüm eğitmenler için ofis atamaları görüntülemek istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="0f179-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="0f179-178">Kullanıcı bir eğitmen seçtiğinde, ilgili Kurs varlıkları görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0f179-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="0f179-179">Eğitmen ve Kurs kuruluşları çok-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="0f179-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="0f179-180">Kurs kuruluşları ve ilgili Bölüm kuruluşları için istekli yükleme kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="0f179-181">Bu durumda, yalnızca seçilen eğitmen için kurslara ihtiyacınız olduğundan ayrı sorgular daha verimli olabilir.</span><span class="sxs-lookup"><span data-stu-id="0f179-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="0f179-182">Ancak, bu örnek, navigasyon özellikleri nde kendileri olan varlıklar içinde gezinme özellikleri için istekli yükleme nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="0f179-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="0f179-183">Kullanıcı bir kurs seçtiğinde, Kayıtlar varlık kümesinden ilgili veriler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0f179-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="0f179-184">Kurs ve Kayıt kuruluşları bir-çok ilişkisi vardır.</span><span class="sxs-lookup"><span data-stu-id="0f179-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="0f179-185">Kayıt kuruluşları ve bunların ilgili Öğrenci varlıkları için ayrı sorgular kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="0f179-186">Eğitmen Dizini görünümü için bir görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="0f179-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="0f179-187">Eğitmenler sayfası üç farklı tablodan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="0f179-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="0f179-188">Bu nedenle, her biri tablolardan birinin verilerini tutan üç özellik içeren bir görünüm modeli oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="0f179-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="0f179-189">*SchoolViewModels* klasöründe, *InstructorIndexData.cs* oluşturun ve varolan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="0f179-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="0f179-190">Eğitmen denetleyicisini ve görünümlerini oluşturun</span><span class="sxs-lookup"><span data-stu-id="0f179-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="0f179-191">Aşağıdaki resimde gösterildiği gibi EF okuma/yazma eylemlerine sahip bir Eğitmen denetleyicisi oluşturun:</span><span class="sxs-lookup"><span data-stu-id="0f179-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Eğitmenler denetleyicisi ekle](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="0f179-193">*InstructorsController.cs* açın ve Görünüm Modelleri ad alanı için bir kullanma deyimi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0f179-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="0f179-194">İlgili verilerin hevesle yüklenmesi için Dizin yöntemini aşağıdaki kodla değiştirin ve görünüm modeline koyun.</span><span class="sxs-lookup"><span data-stu-id="0f179-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="0f179-195">Yöntem, seçilen eğitmenin`id`ve seçilen kursun kimlik`courseID`değerlerini sağlayan isteğe bağlı rota verilerini ( ) ve sorgu dize parametresini ( ) kabul eder.</span><span class="sxs-lookup"><span data-stu-id="0f179-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="0f179-196">Parametreler, sayfadaki **Seç** köprüleri tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="0f179-197">Kod, görünüm modelinin bir örneğini oluşturarak ve eğitmen listesini koyarak başlar.</span><span class="sxs-lookup"><span data-stu-id="0f179-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="0f179-198">Kod ve `Instructor.OfficeAssignment` `Instructor.CourseAssignments` navigasyon özellikleri için istekli yükleme belirtir.</span><span class="sxs-lookup"><span data-stu-id="0f179-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="0f179-199">`CourseAssignments` Özellik içinde, `Course` özellik yüklenir ve bunun `Enrollments` içinde, `Department` ve özellikleri yüklenir ve her `Enrollment` varlık içinde `Student` özellik yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0f179-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="0f179-200">Görünüm her zaman OfficeAssignment tüzel kişiliğini gerektirdiğinden, aynı sorguda bunu almak daha verimliolur.</span><span class="sxs-lookup"><span data-stu-id="0f179-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="0f179-201">Web sayfasında bir eğitmen seçildiğinde ders varlıkları gereklidir, bu nedenle tek bir sorgu birden çok sorgudan daha iyidir, ancak sayfa daha sık bir dersle seçilirse daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="0f179-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="0f179-202">Kod yineler `CourseAssignments` `Course` ve çünkü 'den `Course`iki özelliğe ihtiyacınız vardır.</span><span class="sxs-lookup"><span data-stu-id="0f179-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="0f179-203">İlk `ThenInclude` çağrı dizisi `CourseAssignment.Course`, `Course.Enrollments`ve `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="0f179-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="0f179-204">Kodun bu noktasında, `ThenInclude` başka bir navigasyon `Student`özellikleri için , hangi gerekmez olacaktır.</span><span class="sxs-lookup"><span data-stu-id="0f179-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="0f179-205">Ama `Include` arama özellikleri `Instructor` ile baştan başlar, bu yüzden tekrar zincir `Course.Department` üzerinden `Course.Enrollments`gitmek zorunda, bu kez yerine belirten .</span><span class="sxs-lookup"><span data-stu-id="0f179-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="0f179-206">Bir eğitmen seçildiğinde aşağıdaki kod çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="0f179-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="0f179-207">Seçilen eğitmen görünüm modelindeki eğitmenler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="0f179-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="0f179-208">Görünüm modelinin `Courses` özelliği daha sonra o eğitmenin `CourseAssignments` gezinti özelliğinden Ders varlıklarıyla yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0f179-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="0f179-209">Yöntem `Where` bir koleksiyon döndürür, ancak bu durumda bu yönteme geçirilen ölçütler yalnızca tek bir Eğitmen varlığı döndürülür sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="0f179-210">Yöntem, `Single` koleksiyonu tek bir Eğitmen varlığına dönüştürür ve bu da `CourseAssignments` bu varlığın özelliğine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0f179-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="0f179-211">Özellik, `CourseAssignments` `CourseAssignment` yalnızca ilgili `Course` varlıkları istediğiniz varlıkları içerir.</span><span class="sxs-lookup"><span data-stu-id="0f179-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="0f179-212">Koleksiyonun `Single` yalnızca bir öğesi olacağını bildiğinizde, bir koleksiyonda yöntemi kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="0f179-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="0f179-213">Tek yöntem, koleksiyon boşsa veya birden fazla öğe varsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0f179-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="0f179-214">Alternatif, `SingleOrDefault`koleksiyon boşsa varsayılan değeri (bu durumda null) döndüren dir.</span><span class="sxs-lookup"><span data-stu-id="0f179-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="0f179-215">Ancak, yine de bir özel durum (null reference `Courses` bir özellik bulmaya çalışırken) neden olur ve özel durum iletisi daha az açıkça sorunun nedenini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0f179-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="0f179-216">`Single` Yöntemi aradığınızda, `Where` yöntemi ayrı ayrı aramak yerine Nerede koşulu'ndan da geçebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0f179-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="0f179-217">Onun yerine:</span><span class="sxs-lookup"><span data-stu-id="0f179-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="0f179-218">Daha sonra, bir kurs seçilirse, seçilen kurs görünüm modelindeki dersler listesinden alınır.</span><span class="sxs-lookup"><span data-stu-id="0f179-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="0f179-219">Ardından görünüm modelinin `Enrollments` özelliği, o kursun `Enrollments` gezinti özelliğinden Kayıt varlıklarıyla yüklenir.</span><span class="sxs-lookup"><span data-stu-id="0f179-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="0f179-220">Eğitmen Dizini görünümünü değiştirme</span><span class="sxs-lookup"><span data-stu-id="0f179-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="0f179-221">*Görünümler/Eğitmenler/Index.cshtml'de*şablon kodunu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0f179-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="0f179-222">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="0f179-223">Varolan kodda aşağıdaki değişiklikleri yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0f179-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="0f179-224">Model sınıfını `InstructorIndexData`' niçin değiştirildi</span><span class="sxs-lookup"><span data-stu-id="0f179-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="0f179-225">Sayfa başlığı **Dizin'den** **Eğitmenler'e**değiştirildi.</span><span class="sxs-lookup"><span data-stu-id="0f179-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="0f179-226">Yalnızca null `item.OfficeAssignment` değilse `item.OfficeAssignment.Location` görüntüleyen bir **Office** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="0f179-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="0f179-227">(Bu bire sıfır veya bir ilişkisi olduğundan, ilgili bir OfficeAssignment tüzel kişiliği olmayabilir.)</span><span class="sxs-lookup"><span data-stu-id="0f179-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="0f179-228">Her eğitmen tarafından öğretilen dersleri görüntüleyen bir **Kurslar** sütunu eklendi.</span><span class="sxs-lookup"><span data-stu-id="0f179-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="0f179-229">Daha fazla bilgi için, Razor sözdizimi makalesinin [Açık satır geçiş](xref:mvc/views/razor#explicit-line-transition) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0f179-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="0f179-230">Seçilen eğitmenin `class="success"` `tr` öğesine dinamik olarak eklenen kod eklendi.</span><span class="sxs-lookup"><span data-stu-id="0f179-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="0f179-231">Bu, Bootstrap sınıfını kullanarak seçili satır için bir arka plan rengi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0f179-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="0f179-232">Seçili eğitmenin kimliğinin `Index` yönteme gönderilmesine neden olan, her satırdaki diğer bağlantılardan hemen önce **Seç** etiketli yeni bir köprü eklendi.</span><span class="sxs-lookup"><span data-stu-id="0f179-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="0f179-233">Uygulamayı çalıştırın ve **Eğitmenler** sekmesini seçin. Sayfa, ilgili OfficeAssignment varlıklarının Konum özelliğini ve ilgili OfficeAssignment varlığı olmadığında boş bir tablo hücresini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0f179-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Eğitmenler Dizin sayfası hiçbir şey seçildi](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="0f179-235">*Görünümler/Eğitmenler/Index.cshtml* dosyasında, kapanış tablosu öğesinden sonra (dosyanın sonunda) aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0f179-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="0f179-236">Bu kod, bir eğitmen seçildiğinde bir eğitmenle ilgili derslerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0f179-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="0f179-237">Bu kod, `Courses` bir ders listesini görüntülemek için görünüm modelinin özelliğini okur.</span><span class="sxs-lookup"><span data-stu-id="0f179-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="0f179-238">Ayrıca, seçili kursun kimliğini `Index` eylem yöntemine gönderen bir **Seç** köprü sağlar.</span><span class="sxs-lookup"><span data-stu-id="0f179-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="0f179-239">Sayfayı yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="0f179-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="0f179-240">Şimdi seçilen eğitmene atanan dersleri görüntüleyen bir ızgara görüyorsunuz ve her kurs için atanan bölümün adını görüyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="0f179-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Eğitmenler Dizin sayfası eğitmeni seçildi](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="0f179-242">Eklediğiniz kod bloğundan sonra aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0f179-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="0f179-243">Bu, o ders seçildiğinde bir kursa kayıtlı olan öğrencilerin listesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0f179-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="0f179-244">Bu kod, kursa kayıtlı öğrencilerin listesini görüntülemek için görünüm modelinin Kayıtlar özelliğini okur.</span><span class="sxs-lookup"><span data-stu-id="0f179-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="0f179-245">Sayfayı yeniden yenileyin ve bir eğitmen seçin.</span><span class="sxs-lookup"><span data-stu-id="0f179-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="0f179-246">Ardından, kayıtlı öğrencilerin listesini ve notlarını görmek için bir kurs seçin.</span><span class="sxs-lookup"><span data-stu-id="0f179-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Eğitmenler Dizin sayfa öğretim görevlisi ve seçilen ders](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="0f179-248">Açık yükleme hakkında</span><span class="sxs-lookup"><span data-stu-id="0f179-248">About explicit loading</span></span>

<span data-ttu-id="0f179-249">*InstructorsController.cs'daki*eğitmenlerin listesini aldığınızda, `CourseAssignments` gezinti özelliği için istekli yükleme yi belirttiniz.</span><span class="sxs-lookup"><span data-stu-id="0f179-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="0f179-250">Kullanıcıların belirli bir eğitmen ve kurstaki kayıtları nadiren görmek istemelerini beklediğiniz varsayalım.</span><span class="sxs-lookup"><span data-stu-id="0f179-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="0f179-251">Bu durumda, kayıt verilerini yalnızca istendiğinde yüklemek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0f179-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="0f179-252">Açık yüklemenin nasıl yapılacağını görmek için, `Index` metodun kayıtları ve bu özelliği açıkça yükleyen yüklemeleri kaldıran aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0f179-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="0f179-253">Kod değişiklikleri vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="0f179-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="0f179-254">Yeni kod, Eğitmen varlıklarını alan koddan kayıt verilerini çağıran *ThenInclude* yöntemini düşürür.</span><span class="sxs-lookup"><span data-stu-id="0f179-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="0f179-255">Ayrıca düşer. `AsNoTracking`</span><span class="sxs-lookup"><span data-stu-id="0f179-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="0f179-256">Bir eğitmen ve kurs seçilirse, vurgulanan kod seçilen kurs için Kayıt varlıklarını ve her Kayıt için Öğrenci varlıklarını alır.</span><span class="sxs-lookup"><span data-stu-id="0f179-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="0f179-257">Uygulamayı çalıştırın, şimdi Eğitmenler Dizini sayfasına gidin ve verilerin nasıl alındığını değiştirmiş olsanız da sayfada görüntülenenler arasında hiçbir fark görmezsiniz.</span><span class="sxs-lookup"><span data-stu-id="0f179-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="0f179-258">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="0f179-258">Get the code</span></span>

[<span data-ttu-id="0f179-259">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="0f179-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="0f179-260">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="0f179-260">Next steps</span></span>

<span data-ttu-id="0f179-261">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="0f179-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0f179-262">İlgili verilerin nasıl yüklenir öğrenilir</span><span class="sxs-lookup"><span data-stu-id="0f179-262">Learned how to load related data</span></span>
> * <span data-ttu-id="0f179-263">Kurslar sayfası oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="0f179-263">Created a Courses page</span></span>
> * <span data-ttu-id="0f179-264">Eğitmenler sayfası oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="0f179-264">Created an Instructors page</span></span>
> * <span data-ttu-id="0f179-265">Açık yükleme hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="0f179-265">Learned about explicit loading</span></span>

<span data-ttu-id="0f179-266">İlgili verileri nasıl güncelleştirtini öğrenmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="0f179-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0f179-267">İlgili verileri güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="0f179-267">Update related data</span></span>](update-related-data.md)
