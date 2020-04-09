---
title: 'Öğretici: Gelişmiş senaryolar hakkında bilgi edinin - EF Core ile mvc ASP.NET'
description: Bu öğretici, Entity Framework Core'u kullanan ASP.NET Core web uygulamaları geliştirmenin temellerinin ötesine geçerek yararlı konular sunar.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416235"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="90e47-103">Öğretici: Gelişmiş senaryolar hakkında bilgi edinin - EF Core ile mvc ASP.NET</span><span class="sxs-lookup"><span data-stu-id="90e47-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="90e47-104">Önceki öğreticide, hiyerarşi başına tablo kalıtımını uyguladınız.</span><span class="sxs-lookup"><span data-stu-id="90e47-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="90e47-105">Bu öğretici, Entity Framework Core'u kullanan ASP.NET Temel web uygulamaları geliştirmetemellerinin ötesine geçtiğinde farkında olmak için yararlı olan çeşitli konuları tanıtır.</span><span class="sxs-lookup"><span data-stu-id="90e47-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="90e47-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="90e47-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="90e47-107">Ham SQL sorguları gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="90e47-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="90e47-108">Varlıkları döndürmek için sorgu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-108">Call a query to return entities</span></span>
> * <span data-ttu-id="90e47-109">Diğer türleri döndürmek için sorgu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-109">Call a query to return other types</span></span>
> * <span data-ttu-id="90e47-110">Güncelleştirme sorgusu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-110">Call an update query</span></span>
> * <span data-ttu-id="90e47-111">SQL sorgularını inceleme</span><span class="sxs-lookup"><span data-stu-id="90e47-111">Examine SQL queries</span></span>
> * <span data-ttu-id="90e47-112">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="90e47-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="90e47-113">Otomatik değişiklik algılama hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="90e47-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="90e47-114">EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="90e47-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="90e47-115">Kodu basitleştirmek için dinamik LINQ'yi nasıl kullanacağınızı öğrenin</span><span class="sxs-lookup"><span data-stu-id="90e47-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="90e47-116">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="90e47-116">Prerequisites</span></span>

* [<span data-ttu-id="90e47-117">Kalıtım Uygula</span><span class="sxs-lookup"><span data-stu-id="90e47-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="90e47-118">Ham SQL sorguları gerçekleştirin</span><span class="sxs-lookup"><span data-stu-id="90e47-118">Perform raw SQL queries</span></span>

<span data-ttu-id="90e47-119">Varlık Çerçevesi'ni kullanmanın avantajlarından biri, kodunuzu belirli bir veri depolama yöntemine çok yakın bağlamaktan kaçınmasıdır.</span><span class="sxs-lookup"><span data-stu-id="90e47-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="90e47-120">Bunu sizin için SQL sorguları ve komutları oluşturarak yapar, bu da onları kendiniz yazmak zorunda sizi kurtarır.</span><span class="sxs-lookup"><span data-stu-id="90e47-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="90e47-121">Ancak, el ile oluşturduğunuz belirli SQL sorgularını çalıştırmanız gerektiğinde olağanüstü senaryolar vardır.</span><span class="sxs-lookup"><span data-stu-id="90e47-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="90e47-122">Bu senaryolar için Varlık Çerçeve Kodu İlk API, SQL komutlarını doğrudan veritabanına geçirmenizi sağlayan yöntemler içerir.</span><span class="sxs-lookup"><span data-stu-id="90e47-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="90e47-123">EF Core 1.0'da aşağıdaki seçeneklere sahipsiniz:</span><span class="sxs-lookup"><span data-stu-id="90e47-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="90e47-124">Varlık `DbSet.FromSql` türlerini döndüren sorgular için yöntemi kullanın.</span><span class="sxs-lookup"><span data-stu-id="90e47-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="90e47-125">Döndürülen nesneler `DbSet` nesne tarafından beklenen türden olmalıdır ve siz izlemeyi [kapatmadığınız](crud.md#no-tracking-queries)sürece veritabanı bağlamı tarafından otomatik olarak izlenirler.</span><span class="sxs-lookup"><span data-stu-id="90e47-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="90e47-126">Sorgu `Database.ExecuteSqlCommand` olmayan komutlar için kullanın.</span><span class="sxs-lookup"><span data-stu-id="90e47-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="90e47-127">Varlık olmayan türleri döndüren bir sorgu çalıştırmanız gerekiyorsa, EF tarafından sağlanan veritabanı bağlantısıyla ADO.NET kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="90e47-128">Döndürülen veriler, varlık türlerini almak için bu yöntemi kullansanız bile veritabanı bağlamı tarafından izlenmez.</span><span class="sxs-lookup"><span data-stu-id="90e47-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="90e47-129">Bir web uygulamasında SQL komutlarını uyguladığınızda her zaman olduğu gibi, sitenizi SQL enjeksiyon saldırılarına karşı korumak için önlemler almanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="90e47-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="90e47-130">Bunu yapmanın bir yolu, bir web sayfası tarafından gönderilen dizelerin SQL komutları olarak yorumlanamayacağından emin olmak için parametreli sorgular kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="90e47-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="90e47-131">Bu öğreticide, kullanıcı girişini bir sorguya tümleştirerken parametreli sorgular kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="90e47-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="90e47-132">Varlıkları döndürmek için sorgu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-132">Call a query to return entities</span></span>

<span data-ttu-id="90e47-133">Sınıf, `DbSet<TEntity>` türünden `TEntity`bir varlık döndüren bir sorgu yürütmek için kullanabileceğiniz bir yöntem sağlar.</span><span class="sxs-lookup"><span data-stu-id="90e47-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="90e47-134">Bunun nasıl çalıştığını görmek için Bölüm denetleyicisi yöntemindeki `Details` kodu değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="90e47-135">*DepartmentsController.cs,* `Details` yöntemde, aşağıdaki vurgulanan kodda gösterildiği `FromSql` gibi, bir bölümü bir yöntem çağrısıyla alan kodu değiştirin:</span><span class="sxs-lookup"><span data-stu-id="90e47-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="90e47-136">Yeni kodun doğru çalıştığını doğrulamak için **Bölümler** sekmesini ve bölümlerden birinin **Ayrıntıları'nı** seçin.</span><span class="sxs-lookup"><span data-stu-id="90e47-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Bölüm Detayları](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="90e47-138">Diğer türleri döndürmek için sorgu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-138">Call a query to return other types</span></span>

<span data-ttu-id="90e47-139">Daha önce, her kayıt tarihi için öğrenci sayısını gösteren Hakkında sayfası için bir öğrenci istatistikleri tablosu oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="90e47-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="90e47-140">Verileri Öğrenciler varlık kümesinden aldınız`_context.Students`ve sonuçları `EnrollmentDateGroup` görünüm modeli nesneleri listesine yansıtmak için LINQ'yi kullandınız.</span><span class="sxs-lookup"><span data-stu-id="90e47-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="90e47-141">LINQ kullanmak yerine SQL'in kendisini yazmak istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="90e47-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="90e47-142">Bunu yapmak için varlık nesneleri dışında bir şey döndüren bir SQL sorgusu çalıştırmak gerekir.</span><span class="sxs-lookup"><span data-stu-id="90e47-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="90e47-143">EF Core 1.0'da bunu yapmanın bir yolu ADO.NET kodu yazmak ve veritabanı bağlantısını EF'den almaktır.</span><span class="sxs-lookup"><span data-stu-id="90e47-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="90e47-144">*HomeController.cs*olarak, `About` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="90e47-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="90e47-145">Kullanarak ifade ekle:</span><span class="sxs-lookup"><span data-stu-id="90e47-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="90e47-146">Uygulamayı çalıştırın ve Hakkında sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="90e47-146">Run the app and go to the About page.</span></span> <span data-ttu-id="90e47-147">Daha önce yaptığı verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="90e47-147">It displays the same data it did before.</span></span>

![Sayfa hakkında](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="90e47-149">Güncelleştirme sorgusu çağırma</span><span class="sxs-lookup"><span data-stu-id="90e47-149">Call an update query</span></span>

<span data-ttu-id="90e47-150">Contoso Üniversitesi yöneticilerinin veritabanında her ders için kredi sayısını değiştirmek gibi genel değişiklikler gerçekleştirmek istediklerini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="90e47-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="90e47-151">Üniversitenin çok sayıda dersi varsa, hepsini kurum olarak alıp tek tek değiştirmek verimsiz olacaktır.</span><span class="sxs-lookup"><span data-stu-id="90e47-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="90e47-152">Bu bölümde, kullanıcının tüm derslerin kredi sayısını değiştirmek için bir faktör belirtmesini sağlayan bir web sayfası uygularsınız ve bir SQL UPDATE bildirimi uygulayarak değişikliği yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="90e47-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="90e47-153">Web sayfası aşağıdaki çizimgibi görünecektir:</span><span class="sxs-lookup"><span data-stu-id="90e47-153">The web page will look like the following illustration:</span></span>

![Ders Kredileri sayfasını güncelle](advanced/_static/update-credits.png)

<span data-ttu-id="90e47-155">*CoursesController.cs*yılında, HttpGet ve HttpPost için UpdateCourseCredits yöntemleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="90e47-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="90e47-156">Denetleyici bir HttpGet isteğini işlediğinde, `ViewData["RowsAffected"]`hiçbir şey döndürülür ve görünüm önceki resimde gösterildiği gibi boş bir metin kutusu ve gönder düğmesi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="90e47-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="90e47-157">**Güncelleştirme** düğmesi tıklandığında, HttpPost yöntemi çağrılır ve çarpan metin kutusuna girilen değere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="90e47-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="90e47-158">Kod daha sonra dersleri güncelleyen ve etkilenen satır sayısını görünüme `ViewData`döndüren SQL'i yürütür.</span><span class="sxs-lookup"><span data-stu-id="90e47-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="90e47-159">Görünüm bir `RowsAffected` değer aldığında, güncelleştirilen satır sayısını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="90e47-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="90e47-160">**Çözüm Gezgini'nde** *Görünümler/Kurslar* klasörüne sağ tıklayın ve ardından **Yeni Öğe > ekle'yi**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="90e47-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="90e47-161">Yeni **Öğe Ekle** iletişim kutusunda, sol bölmede **Yüklü** ASP.NET **Core'u** tıklatın, **Razor View'ı**tıklatın ve yeni görünümü *UpdateCourseCredits.cshtml*adını alın.</span><span class="sxs-lookup"><span data-stu-id="90e47-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="90e47-162">*Görünümler/Kurslar/UpdateCourseCredits.cshtml'de*şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="90e47-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="90e47-163">`UpdateCourseCredits` **Kurslar** sekmesini seçerek yöntemi çalıştırın ve ardından tarayıcının adres çubuğundaki URL'nin sonuna "/UpdateCourseCredits" ekleyerek (örneğin: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="90e47-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="90e47-164">Metin kutusuna bir numara girin:</span><span class="sxs-lookup"><span data-stu-id="90e47-164">Enter a number in the text box:</span></span>

![Ders Kredileri sayfasını güncelle](advanced/_static/update-credits.png)

<span data-ttu-id="90e47-166">**Güncelleştir**’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-166">Click **Update**.</span></span> <span data-ttu-id="90e47-167">Etkilenen satır sayısını görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="90e47-167">You see the number of rows affected:</span></span>

![Etkilenen Ders Kredileri sayfa satırlarını güncelleştirme](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="90e47-169">Kredi sayısı revize edilmiş derslerin listesini görmek için **Listeye Geri Dön'ü** tıklayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="90e47-170">Üretim kodunun güncelleştirmeleri her zaman geçerli verilerle sonuçlandırmasını sağlayacağını unutmayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="90e47-171">Burada gösterilen basitleştirilmiş kod, 5'ten büyük sayılarla sonuçlanmasıiçin yeterli olan kredi sayısını çarpabilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="90e47-172">(Özelliğin `Credits` bir `[Range(0, 5)]` özelliği vardır.) Güncelleştirme sorgusu çalışır, ancak geçersiz veriler, kredi sayısının 5 veya daha az olduğunu varsayan sistemin diğer bölümlerinde beklenmeyen sonuçlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="90e47-173">Ham SQL sorguları hakkında daha fazla bilgi için [Raw SQL Sorguları'na](/ef/core/querying/raw-sql)bakın.</span><span class="sxs-lookup"><span data-stu-id="90e47-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="90e47-174">SQL sorgularını inceleme</span><span class="sxs-lookup"><span data-stu-id="90e47-174">Examine SQL queries</span></span>

<span data-ttu-id="90e47-175">Bazen veritabanına gönderilen gerçek SQL sorgularını görebilmek yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="90e47-176">ASP.NET Core için yerleşik günlük işlevi, EF Core tarafından sorgular ve güncelleştirmeler için SQL içeren günlükleri yazmak için otomatik olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="90e47-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="90e47-177">Bu bölümde SQL günlük bazı örnekler görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="90e47-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="90e47-178">*Açık StudentsController.cs* ve `Details` yöntemde `if (student == null)` deyimi bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="90e47-179">Uygulamayı hata ayıklama modunda çalıştırın ve bir öğrencinin Ayrıntılar sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="90e47-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="90e47-180">Hata ayıklama çıktısını gösteren **Çıktı** penceresine gidin ve sorguyu görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="90e47-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="90e47-181">Burada sizi şaşırtabilecek bir şey fark edeceksiniz: SQL Kişi`TOP(2)`tablosundan en fazla 2 satır ( ) seçer.</span><span class="sxs-lookup"><span data-stu-id="90e47-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="90e47-182">Yöntem, `SingleOrDefaultAsync` sunucuda 1 satıra çözülmüyor.</span><span class="sxs-lookup"><span data-stu-id="90e47-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="90e47-183">Nedeni:</span><span class="sxs-lookup"><span data-stu-id="90e47-183">Here's why:</span></span>

* <span data-ttu-id="90e47-184">Sorgu birden çok satır döndürürse, yöntem null döndürür.</span><span class="sxs-lookup"><span data-stu-id="90e47-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="90e47-185">Sorgunun birden çok satır döndürüp döndürmeyeceğini belirlemek için, EF'nin en az 2 satır döndürüp döndürmediğini denetlemesi gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="90e47-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="90e47-186">**Çıktı** penceresinde günlüğe kaydetme çıktısı almak için hata ayıklama modunu kullanmanız ve kesme noktasında durmanız gerekmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="90e47-187">Bu, çıkışa bakmak istediğiniz noktada günlüğe kaydetmeyi durdurmanın kullanışlı bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="90e47-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="90e47-188">Bunu yapmazsanız, günlüğe kaydetme devam eder ve ilgilendiğiniz parçaları bulmak için geri kaydırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="90e47-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="90e47-189">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="90e47-189">Create an abstraction layer</span></span>

<span data-ttu-id="90e47-190">Birçok geliştirici, Varlık Çerçevesi ile çalışan kod etrafında bir sarmalayıcı olarak çalışma kalıplarının deposunu ve birimini uygulamak için kod yazar.</span><span class="sxs-lookup"><span data-stu-id="90e47-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="90e47-191">Bu desenler, veri erişim katmanı ile uygulamanın iş mantığı katmanı arasında bir soyutlama katmanı oluşturmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="90e47-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="90e47-192">Bu desenlerin uygulanması, uygulamanızın veri deposundaki değişikliklerden izole edilmesine yardımcı olabilir ve otomatik birim testini veya test odaklı geliştirmeyi (TDD) kolaylaştırabilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="90e47-193">Ancak, bu desenleri uygulamak için ek kod yazmak, çeşitli nedenlerden dolayı EF kullanan uygulamalar için her zaman en iyi seçim değildir:</span><span class="sxs-lookup"><span data-stu-id="90e47-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="90e47-194">EF bağlam sınıfının kendisi kodunuzu veri deposuna özgü koddan yalıtır.</span><span class="sxs-lookup"><span data-stu-id="90e47-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="90e47-195">EF bağlam sınıfı, EF kullanarak yaptığınız veritabanı güncelleştirmeleri için bir çalışma birimi sınıfı olarak hareket edebilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="90e47-196">EF, depo kodu yazmadan TDD'yi uygulamak için özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="90e47-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="90e47-197">Çalışma kalıplarının deposu ve birimi hakkında bilgi için [bu öğretici serinin Entity Framework 5 sürümüne](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)bakın.</span><span class="sxs-lookup"><span data-stu-id="90e47-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="90e47-198">Varlık Framework Core, sınama için kullanılabilecek bir bellek içi veritabanı sağlayıcısı uygular.</span><span class="sxs-lookup"><span data-stu-id="90e47-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="90e47-199">Daha fazla bilgi için [InMemory ile Sınama'ya](/ef/core/miscellaneous/testing/in-memory)bakın.</span><span class="sxs-lookup"><span data-stu-id="90e47-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="90e47-200">Otomatik değişiklik algılama</span><span class="sxs-lookup"><span data-stu-id="90e47-200">Automatic change detection</span></span>

<span data-ttu-id="90e47-201">Varlık Çerçevesi, bir varlığın geçerli değerlerini özgün değerlerle karşılaştırarak bir varlığın nasıl değiştiğini (ve bu nedenle hangi güncelleştirmelerin veritabanına gönderilmesi gerektiğini) belirler.</span><span class="sxs-lookup"><span data-stu-id="90e47-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="90e47-202">Varlık sorgulandığında veya eklendiğinde özgün değerler depolanır.</span><span class="sxs-lookup"><span data-stu-id="90e47-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="90e47-203">Otomatik değişiklik algılamaya neden olan yöntemlerden bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="90e47-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="90e47-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="90e47-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="90e47-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="90e47-205">DbContext.Entry</span></span>

* <span data-ttu-id="90e47-206">ChangeTracker.Girişleri</span><span class="sxs-lookup"><span data-stu-id="90e47-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="90e47-207">Çok sayıda varlığı takip ediyorsanız ve bu yöntemlerden birini döngü içinde birçok kez arıyorsanız, `ChangeTracker.AutoDetectChangesEnabled` özelliği kullanarak otomatik değiştirme algılamasını geçici olarak kapatarak önemli performans iyileştirmeleri elde edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="90e47-208">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="90e47-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="90e47-209">EF Çekirdek kaynak kodu ve geliştirme planları</span><span class="sxs-lookup"><span data-stu-id="90e47-209">EF Core source code and development plans</span></span>

<span data-ttu-id="90e47-210">Varlık Çerçeve Çekirdek kaynağı [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="90e47-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="90e47-211">EF Core deposu, gece yapılarını, sorun izlemeyi, özellik özelliklerini, tasarım toplantı notlarını ve [gelecekteki gelişim için yol haritasını](https://github.com/dotnet/efcore/wiki/Roadmap)içerir.</span><span class="sxs-lookup"><span data-stu-id="90e47-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="90e47-212">Dosyalayabilir veya hataları bulabilir ve katkıda bulunabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="90e47-213">Kaynak kodu açık olmasına rağmen, Entity Framework Core bir Microsoft ürünü olarak tam olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="90e47-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="90e47-214">Microsoft Entity Framework ekibi, hangi katkıların kabul edildiği üzerinde denetim tutar ve her sürümün kalitesini sağlamak için tüm kod değişikliklerini sınar.</span><span class="sxs-lookup"><span data-stu-id="90e47-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="90e47-215">Varolan veritabanından ters mühendis</span><span class="sxs-lookup"><span data-stu-id="90e47-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="90e47-216">Varolan bir veritabanındaki varlık sınıflarını içeren bir veri modelini tersine çevirmek için [iskele-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="90e47-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="90e47-217">Başlangıç [öğreticisini](/ef/core/get-started/aspnetcore/existing-db)görün.</span><span class="sxs-lookup"><span data-stu-id="90e47-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="90e47-218">Kodu basitleştirmek için dinamik LINQ'yi kullanın</span><span class="sxs-lookup"><span data-stu-id="90e47-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="90e47-219">[Bu serinin üçüncü öğretici](sort-filter-page.md) nasıl bir `switch` deyim de sert kodlama sütun adlarıyla LINQ kodu yazmak için gösterir.</span><span class="sxs-lookup"><span data-stu-id="90e47-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="90e47-220">Aralarından seçim yapabileceğiniz iki sütunla, bu iyi çalışır, ancak çok sayıda sütununuzun varsa kod ayrıntılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="90e47-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="90e47-221">Bu sorunu çözmek için, `EF.Property` özelliğin adını dize olarak belirtmek için yöntemi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="90e47-222">Bu yaklaşımı denemek için, `Index` aşağıdaki `StudentsController` kodla yöntemi değiştirin.</span><span class="sxs-lookup"><span data-stu-id="90e47-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="90e47-223">İlgili kaynaklar</span><span class="sxs-lookup"><span data-stu-id="90e47-223">Acknowledgments</span></span>

<span data-ttu-id="90e47-224">Tom Dykstra ve Rick @RickAndMSFTAnderson (twitter) bu öğretici yazdı.</span><span class="sxs-lookup"><span data-stu-id="90e47-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="90e47-225">Rowan Miller, Diego Vega ve Entity Framework ekibinin diğer üyeleri kod incelemeleri ile yardımcı oldu ve biz öğreticiler için kod yazarken ortaya çıkan hata ayıklama sorunları yardımcı oldu.</span><span class="sxs-lookup"><span data-stu-id="90e47-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="90e47-226">John Parente ve Paul Goldman ASP.NET Core 2.2 için öğretici güncelleme üzerinde çalıştı.</span><span class="sxs-lookup"><span data-stu-id="90e47-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="90e47-227">Sık karşılaşılan hataları giderme</span><span class="sxs-lookup"><span data-stu-id="90e47-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="90e47-228">ContosoUniversity.dll başka bir işlem tarafından kullanılan</span><span class="sxs-lookup"><span data-stu-id="90e47-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="90e47-229">Hata iletisi:</span><span class="sxs-lookup"><span data-stu-id="90e47-229">Error message:</span></span>

> <span data-ttu-id="90e47-230">Açılmıyor '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' yazmak için -- 'İşlem başka bir işlem tarafından kullanıldığından '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' dosyasına erişemez.</span><span class="sxs-lookup"><span data-stu-id="90e47-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="90e47-231">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="90e47-231">Solution:</span></span>

<span data-ttu-id="90e47-232">Siteyi IIS Express'te durdurun.</span><span class="sxs-lookup"><span data-stu-id="90e47-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="90e47-233">Windows Sistem Tepsisi'ne gidin, IIS Express'i bulun ve simgesine sağ tıklayın, Contoso Üniversitesi sitesini seçin ve ardından **Siteyi Durdur'u**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="90e47-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="90e47-234">Yukarı ve Aşağı yöntemlerinde kod suz geçiş</span><span class="sxs-lookup"><span data-stu-id="90e47-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="90e47-235">Olası neden:</span><span class="sxs-lookup"><span data-stu-id="90e47-235">Possible cause:</span></span>

<span data-ttu-id="90e47-236">EF CLI komutları kod dosyalarını otomatik olarak kapatmaz ve kaydetmez.</span><span class="sxs-lookup"><span data-stu-id="90e47-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="90e47-237">`migrations add` Komutu çalıştırdığınızda kaydedilmemiş değişiklikleriniz varsa, EF değişikliklerinizi bulamaz.</span><span class="sxs-lookup"><span data-stu-id="90e47-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="90e47-238">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="90e47-238">Solution:</span></span>

<span data-ttu-id="90e47-239">Komutu `migrations remove` çalıştırın, kod değişikliklerinizi `migrations add` kaydedin ve komutu yeniden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="90e47-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="90e47-240">Veritabanı güncelleştirmesi çalıştırırken hatalar</span><span class="sxs-lookup"><span data-stu-id="90e47-240">Errors while running database update</span></span>

<span data-ttu-id="90e47-241">Varolan verilere sahip bir veritabanında şema değişiklikleri yaparken başka hatalar da almak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="90e47-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="90e47-242">Çözemediğiniz geçiş hataları alırsanız, bağlantı dizesinde veritabanı adını değiştirebilir veya veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="90e47-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="90e47-243">Yeni bir veritabanıile, geçirilen veri yoktur ve güncelleştirme veritabanı komutu hatasız olarak tamamlanma olasılığı çok daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="90e47-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="90e47-244">En basit yaklaşım *appsettings.json*veritabanını yeniden adlandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="90e47-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="90e47-245">Bir sonraki çalıştırdığınızda, `database update`yeni bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="90e47-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="90e47-246">SSOX'ta bir veritabanını silmek için veritabanını sağ tıklatın, **Sil'i**tıklatın ve ardından **Veritabanını Sil** iletişim kutusunda **varolan bağlantıları kapat'ı** seçin ve **Tamam'ı**tıklatın.</span><span class="sxs-lookup"><span data-stu-id="90e47-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="90e47-247">CLI'yi kullanarak bir veritabanını `database drop` silmek için CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="90e47-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="90e47-248">SQL Server örneğini bulma hatası</span><span class="sxs-lookup"><span data-stu-id="90e47-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="90e47-249">Hata İletisi:</span><span class="sxs-lookup"><span data-stu-id="90e47-249">Error Message:</span></span>

> <span data-ttu-id="90e47-250">SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="90e47-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="90e47-251">Sunucu bulunamadı veya erişilebilir değildi.</span><span class="sxs-lookup"><span data-stu-id="90e47-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="90e47-252">Örnek adının doğru olduğunu ve SQL Server'ın uzak bağlantılara izin verecek şekilde yapılandırıldığından doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="90e47-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="90e47-253">(sağlayıcı: SQL Ağ Arayüzleri, hata: 26 - Hata Konumlandırma Server/Instance Specified)</span><span class="sxs-lookup"><span data-stu-id="90e47-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="90e47-254">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="90e47-254">Solution:</span></span>

<span data-ttu-id="90e47-255">Bağlantı dizesini kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="90e47-255">Check the connection string.</span></span> <span data-ttu-id="90e47-256">Veritabanı dosyasını el ile sildiyseniz, yeni bir veritabanıyla baştan başlamak için yapı dizesinde veritabanının adını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="90e47-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="90e47-257">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="90e47-257">Get the code</span></span>

[<span data-ttu-id="90e47-258">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="90e47-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="90e47-259">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="90e47-259">Additional resources</span></span>

<span data-ttu-id="90e47-260">EF Core hakkında daha fazla bilgi için [Entity Framework Core belgelerine](/ef/core)bakın.</span><span class="sxs-lookup"><span data-stu-id="90e47-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="90e47-261">Bir kitap da mevcuttur: [Varlık Çerçeve Çekirdek Eylem](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="90e47-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="90e47-262">Bir web uygulamasının nasıl dağıtılanöğretilen hakkında bilgi için bkz. <xref:host-and-deploy/index></span><span class="sxs-lookup"><span data-stu-id="90e47-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="90e47-263">Kimlik doğrulama ve yetkilendirme gibi core MVC ASP.NET ilgili diğer <xref:index>konular hakkında bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="90e47-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="90e47-264">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="90e47-264">Next steps</span></span>

<span data-ttu-id="90e47-265">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="90e47-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="90e47-266">Gerçekleştirilen ham SQL sorguları</span><span class="sxs-lookup"><span data-stu-id="90e47-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="90e47-267">Varlıkları döndürmek için sorgu çağrıldı</span><span class="sxs-lookup"><span data-stu-id="90e47-267">Called a query to return entities</span></span>
> * <span data-ttu-id="90e47-268">Diğer türleri döndürmek için sorgu çağrıldı</span><span class="sxs-lookup"><span data-stu-id="90e47-268">Called a query to return other types</span></span>
> * <span data-ttu-id="90e47-269">Güncelleştirme sorgusu olarak adlandırılır</span><span class="sxs-lookup"><span data-stu-id="90e47-269">Called an update query</span></span>
> * <span data-ttu-id="90e47-270">İncelenen SQL sorguları</span><span class="sxs-lookup"><span data-stu-id="90e47-270">Examined SQL queries</span></span>
> * <span data-ttu-id="90e47-271">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="90e47-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="90e47-272">Otomatik değişiklik algılama hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="90e47-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="90e47-273">EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="90e47-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="90e47-274">Kodu basitleştirmek için dinamik LINQ'nun nasıl kullanılacağını öğrendi</span><span class="sxs-lookup"><span data-stu-id="90e47-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="90e47-275">Bu, ASP.NET Core MVC uygulamasında Entity Framework Core'u kullanma yla ilgili bu öğretici serisini tamamlar.</span><span class="sxs-lookup"><span data-stu-id="90e47-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="90e47-276">Bu seri yeni bir veritabanı ile çalıştı; bir alternatif varolan bir veritabanından bir model ters mühendislik etmektir.</span><span class="sxs-lookup"><span data-stu-id="90e47-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="90e47-277">Öğretici: MVC ile EF Core, mevcut veritabanı</span><span class="sxs-lookup"><span data-stu-id="90e47-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
