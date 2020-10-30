---
title: 'Öğretici: Gelişmiş senaryolar hakkında bilgi edinin-EF Core ASP.NET MVC'
description: Bu öğreticide, Entity Framework Core kullanan ASP.NET Core Web uygulamaları geliştirmeye yönelik temel bilgilerin ötesinde yararlı konular sunulmaktadır.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054391"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="3a8e1-103">Öğretici: Gelişmiş senaryolar hakkında bilgi edinin-EF Core ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="3a8e1-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="3a8e1-104">Önceki öğreticide, tablo başına devralma devralmayı uyguladık.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="3a8e1-105">Bu öğreticide, Entity Framework Core kullanan ASP.NET Core Web uygulamaları geliştirme hakkında temel bilgileri aşdığınızda dikkat etmeniz yararlı olan birkaç konu sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="3a8e1-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a8e1-107">Ham SQL sorguları gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="3a8e1-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="3a8e1-108">Varlıkları döndürmek için bir sorgu çağırın</span><span class="sxs-lookup"><span data-stu-id="3a8e1-108">Call a query to return entities</span></span>
> * <span data-ttu-id="3a8e1-109">Başka türler döndürmek için bir sorgu çağırın</span><span class="sxs-lookup"><span data-stu-id="3a8e1-109">Call a query to return other types</span></span>
> * <span data-ttu-id="3a8e1-110">Güncelleştirme sorgusu çağırma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-110">Call an update query</span></span>
> * <span data-ttu-id="3a8e1-111">SQL sorgularını inceleyin</span><span class="sxs-lookup"><span data-stu-id="3a8e1-111">Examine SQL queries</span></span>
> * <span data-ttu-id="3a8e1-112">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="3a8e1-113">Otomatik değişiklik algılama hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="3a8e1-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="3a8e1-114">EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="3a8e1-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="3a8e1-115">Kodu basitleştirmek için dinamik LINQ kullanmayı öğrenin</span><span class="sxs-lookup"><span data-stu-id="3a8e1-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3a8e1-116">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="3a8e1-116">Prerequisites</span></span>

* [<span data-ttu-id="3a8e1-117">Devralmayı Uygula</span><span class="sxs-lookup"><span data-stu-id="3a8e1-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="3a8e1-118">Ham SQL sorguları gerçekleştirme</span><span class="sxs-lookup"><span data-stu-id="3a8e1-118">Perform raw SQL queries</span></span>

<span data-ttu-id="3a8e1-119">Entity Framework kullanmanın avantajlarından biri, kodunuzun veri depolarken belirli bir yönteme çok benzemesidir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="3a8e1-120">Bunu sizin için SQL sorguları ve komutları oluşturarak yapar, bu da sizi kendiniz yazmak zorunda kalmaktan kurtarır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="3a8e1-121">Ancak el ile oluşturduğunuz belirli SQL sorgularını çalıştırmanız gerektiğinde olağanüstü senaryolar vardır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="3a8e1-122">Bu senaryolar için Entity Framework Code First API 'SI, SQL komutlarını doğrudan veritabanına geçirmenize olanak sağlayan yöntemleri içerir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="3a8e1-123">EF Core 1,0 ' de aşağıdaki seçenekleriniz vardır:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="3a8e1-124">`DbSet.FromSql`Varlık türleri döndüren sorgular için yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="3a8e1-125">Döndürülen nesneler nesne tarafından beklenen türde olmalıdır `DbSet` ve [izlemeyi kapatmadığınız](crud.md#no-tracking-queries)takdirde veritabanı bağlamı tarafından otomatik olarak izlenir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="3a8e1-126">`Database.ExecuteSqlCommand`Sorgu olmayan komutları için kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="3a8e1-127">Varlık olmayan türleri döndüren bir sorgu çalıştırmanız gerekiyorsa, EF tarafından sunulan veritabanı bağlantısıyla ADO.NET kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="3a8e1-128">Döndürülen veriler, varlık türlerini almak için bu yöntemi kullanıyor olsanız bile veritabanı bağlamı tarafından izlenmez.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="3a8e1-129">Her zaman doğru olduğu gibi, bir Web uygulamasında SQL komutları yürüttüğünüzde, sitenizi SQL ekleme saldırılarına karşı korumak için önlemler almalısınız.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="3a8e1-130">Bunu yapmanın bir yolu, bir Web sayfası tarafından gönderilen dizelerin SQL komutları olarak yorumlanamadığından emin olmak için parametreli sorgular kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="3a8e1-131">Bu öğreticide Kullanıcı girişini bir sorguyla tümleştirdiğinizde parametreli sorgular kullanacaksınız.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="3a8e1-132">Varlıkları döndürmek için bir sorgu çağırın</span><span class="sxs-lookup"><span data-stu-id="3a8e1-132">Call a query to return entities</span></span>

<span data-ttu-id="3a8e1-133">`DbSet<TEntity>`Sınıfı, türünde bir varlık döndüren bir sorguyu yürütmek için kullanabileceğiniz bir yöntem sağlar `TEntity` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="3a8e1-134">Bunun nasıl çalıştığını görmek için, `Details` bölüm denetleyicisinin yöntemindeki kodu değiştirirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="3a8e1-135">*DepartmentsController.cs* ' de, `Details` yönteminde, `FromSql` aşağıdaki vurgulanmış kodda gösterildiği gibi, bir departmanı alan kodu bir yöntem çağrısıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-135">In *DepartmentsController.cs* , in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="3a8e1-136">Yeni kodun doğru şekilde çalıştığını doğrulamak için **Departmanlar** sekmesini seçin ve sonra departmanlardan birine ilişkin **ayrıntıları** izleyin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Departman ayrıntıları](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="3a8e1-138">Başka türler döndürmek için bir sorgu çağırın</span><span class="sxs-lookup"><span data-stu-id="3a8e1-138">Call a query to return other types</span></span>

<span data-ttu-id="3a8e1-139">Daha önce, yaklaşık bir kayıt tarihi için öğrenci sayısını gösteren hakkında sayfasında bir öğrenci istatistikleri Kılavuzu oluşturdunuz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="3a8e1-140">Öğrenciler varlık kümesindeki () verileri aldınız `_context.Students` ve sonuçları `EnrollmentDateGroup` görüntüleme modeli nesneleri listesine eklemek için LINQ 'ı kullandınız.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="3a8e1-141">LINQ kullanmak yerine SQL 'in kendisini yazmak istediğinizi varsayalım.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="3a8e1-142">Bunu yapmak için, varlık nesnelerinden başka bir şey döndüren bir SQL sorgusu çalıştırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="3a8e1-143">EF Core 1,0 ' de, bunu yapmanın bir yolu ADO.NET kodunu yazıp EF 'ten veritabanı bağlantısı almanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="3a8e1-144">*HomeController.cs* içinde, `About` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-144">In *HomeController.cs* , replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="3a8e1-145">Using deyimleri ekleme:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="3a8e1-146">Uygulamayı çalıştırın ve hakkında sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-146">Run the app and go to the About page.</span></span> <span data-ttu-id="3a8e1-147">Daha önce yaptığımız verileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-147">It displays the same data it did before.</span></span>

![Sayfa hakkında](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="3a8e1-149">Güncelleştirme sorgusu çağırma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-149">Call an update query</span></span>

<span data-ttu-id="3a8e1-150">Contoso Üniversitesi yöneticilerinin veritabanında, her kurs için kredi sayısını değiştirme gibi genel değişiklikleri gerçekleştirmesini istediğini varsayalım.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="3a8e1-151">Üniversitenin çok sayıda kursu varsa, bunların tümünü varlıklar olarak almak ve tek tek değiştirmek verimsiz olur.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="3a8e1-152">Bu bölümde, kullanıcının tüm kurslar için kredi sayısını değiştirecek bir faktör belirtmesini sağlayan bir Web sayfası uygulayacaksınız ve bir SQL UPDATE ifadesini yürüterek değişikliği yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="3a8e1-153">Web sayfası aşağıdaki çizimde gösterildiği gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-153">The web page will look like the following illustration:</span></span>

![Kurs kredileri sayfasını Güncelleştir](advanced/_static/update-credits.png)

<span data-ttu-id="3a8e1-155">*CoursesController.cs* ' de, HttpGet ve HttpPost için UpdateCourseCredits yöntemleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-155">In *CoursesController.cs* , add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="3a8e1-156">Denetleyici bir HttpGet isteğini işlediğinde, içinde hiçbir şey döndürülmez `ViewData["RowsAffected"]` ve görünüm, önceki çizimde gösterildiği gibi boş bir metin kutusu ve bir Gönder düğmesi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="3a8e1-157">**Update** düğmesine tıklandığında, HttpPost yöntemi çağırılır ve Multiplier metin kutusuna girilen değer vardır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="3a8e1-158">Kod daha sonra, kursu güncelleştiren SQL 'i yürütür ve etkilenen satır sayısını içinde görünüme döndürür `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="3a8e1-159">Görünüm bir `RowsAffected` değer aldığında, güncelleştirilmiş satır sayısını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="3a8e1-160">**Çözüm Gezgini** , *Görünümler/kurslar* klasörüne sağ tıklayın ve ardından **> yeni öğe Ekle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-160">In **Solution Explorer** , right-click the *Views/Courses* folder, and then click **Add > New Item** .</span></span>

<span data-ttu-id="3a8e1-161">**Yeni öğe Ekle** iletişim kutusunda sol bölmede yüklü **ASP.NET Core** ' **Installed** a tıklayın, **:::no-loc(Razor)::: görüntüle** ' ye tıklayın ve yeni görünüm *UpdateCourseCredits. cshtml* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **:::no-loc(Razor)::: View** , and name the new view *UpdateCourseCredits.cshtml* .</span></span>

<span data-ttu-id="3a8e1-162">*Views/kurslar/UpdateCourseCredits. cshtml* içinde, şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-162">In *Views/Courses/UpdateCourseCredits.cshtml* , replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="3a8e1-163">`UpdateCourseCredits` **Kurs** sekmesini seçerek yöntemi çalıştırın, sonra TARAYıCıNıN adres çubuğundaki URL 'nin sonuna "/UpdateCourseCredits" ekleyin (örneğin: `http://localhost:5813/Courses/UpdateCourseCredits` ).</span><span class="sxs-lookup"><span data-stu-id="3a8e1-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="3a8e1-164">Metin kutusuna bir sayı girin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-164">Enter a number in the text box:</span></span>

![Kurs kredileri sayfasını Güncelleştir](advanced/_static/update-credits.png)

<span data-ttu-id="3a8e1-166">**Güncelleştir** ’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-166">Click **Update** .</span></span> <span data-ttu-id="3a8e1-167">Etkilenen satır sayısını görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-167">You see the number of rows affected:</span></span>

![Güncelleştirme kursu kredileri sayfa satırları etkilendi](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="3a8e1-169">Düzeltilen kredi sayısına sahip kurslar listesini görmek için **listeye geri** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="3a8e1-170">Üretim kodunun güncelleştirmelerin her zaman geçerli verilerle sonuçlandığına emin olun.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="3a8e1-171">Burada gösterilen basitleştirilmiş kod, 5 ' ten fazla sayı ile sonuçlanacak kredilerin sayısını çarpamaz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="3a8e1-172">( `Credits` Özelliğin bir özniteliği vardır `[Range(0, 5)]` .) Güncelleştirme sorgusu çalışır, ancak geçersiz veriler sistemin diğer bölümlerinde, kredi sayısının 5 veya daha az olduğunu varsayacak beklenmedik sonuçlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="3a8e1-173">Ham SQL sorguları hakkında daha fazla bilgi için bkz. [Ham SQL sorguları](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="3a8e1-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="3a8e1-174">SQL sorgularını inceleyin</span><span class="sxs-lookup"><span data-stu-id="3a8e1-174">Examine SQL queries</span></span>

<span data-ttu-id="3a8e1-175">Bazen veritabanına gönderilen gerçek SQL sorgularını görmeniz yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="3a8e1-176">ASP.NET Core için yerleşik günlük işlevselliği, sorgular ve güncelleştirmeler için SQL içeren günlükleri yazmak üzere EF Core tarafından otomatik olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="3a8e1-177">Bu bölümde, SQL günlüğe kaydetme işleminin bazı örneklerini görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="3a8e1-178">*StudentsController.cs* ' i açın ve `Details` yöntemi içinde, bildiriminde bir kesme noktası ayarlayın `if (student == null)` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="3a8e1-179">Uygulamayı hata ayıklama modunda çalıştırın ve bir öğrenci için ayrıntılar sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="3a8e1-180">Hata ayıklama çıkışını gösteren **Çıkış** penceresine gidin ve sorguyu görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="3a8e1-181">Size beklenmedik bir şekilde bir sorun olduğunu fark edeceksiniz: SQL, kişi tablosundan 2 ' ye kadar satır ( `TOP(2)` ) seçer.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="3a8e1-182">`SingleOrDefaultAsync`Yöntem, sunucuda 1 satıra çözümlenmiyor.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="3a8e1-183">Nedeni:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-183">Here's why:</span></span>

* <span data-ttu-id="3a8e1-184">Sorgu birden çok satır döndürürse, yöntemi null döndürür.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="3a8e1-185">Sorgunun birden çok satır döndürüp döndürmeyeceğini anlamak için EF 'in en az 2 değerini döndürüp döndürmediğini denetlemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="3a8e1-186">**Çıkış** penceresinde günlüğe kaydetme çıktısını almak için hata ayıklama modunu kullanmanız ve bir kesme noktasında durdurmanız gerekmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="3a8e1-187">Bu, çıkışa bakmak istediğiniz noktada günlüğe kaydetmeyi durdurmak için kullanışlı bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="3a8e1-188">Bunu yapmazsanız, günlüğe kaydetme devam eder ve ilgilendiğiniz parçaları bulmak için geri kaydırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="3a8e1-189">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-189">Create an abstraction layer</span></span>

<span data-ttu-id="3a8e1-190">Birçok geliştirici, Entity Framework ile çalışan kodun etrafında bir sarmalayıcı olarak depo ve iş birimi düzenlerini uygulamak için kod yazar.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="3a8e1-191">Bu desenler, veri erişim katmanı ve bir uygulamanın iş mantığı katmanı arasında bir soyutlama katmanı oluşturmak için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="3a8e1-192">Bu desenleri uygulamak, uygulamanızın veri deposundaki değişikliklerden yalıtılmış hale getirmenize yardımcı olabilir ve otomatik birim testi veya test odaklı geliştirmeyi (TDD) kolaylaştırabilir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="3a8e1-193">Ancak, bu desenleri uygulamak için ek kod yazmak, birkaç nedenden dolayı EF kullanan uygulamalar için her zaman en iyi seçimdir:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="3a8e1-194">EF bağlam sınıfının kendisi, veri deposuna özgü koddan kodunuzun kendisini uygular.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="3a8e1-195">EF bağlam sınıfı, EF kullanarak yaptığınız veritabanı güncelleştirmeleri için bir iş birimi sınıfı işlevi görebilir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="3a8e1-196">EF, depo kodu yazmadan TDD uygulamaya yönelik özellikler içerir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="3a8e1-197">Deponun ve iş düzeni birimlerinin nasıl uygulanacağı hakkında bilgi için, [Bu öğretici serisinin Entity Framework 5 sürümüne](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)bakın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="3a8e1-198">Entity Framework Core, test için kullanılabilecek bir bellek içi veritabanı sağlayıcısı uygular.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="3a8e1-199">Daha fazla bilgi için bkz. [InMemory Ile test](/ef/core/miscellaneous/testing/in-memory)etme.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="3a8e1-200">Otomatik değişiklik algılama</span><span class="sxs-lookup"><span data-stu-id="3a8e1-200">Automatic change detection</span></span>

<span data-ttu-id="3a8e1-201">Entity Framework bir varlığın geçerli değerlerini özgün değerlerle karşılaştırarak bir varlığın nasıl değiştiğini (ve bu nedenle veritabanına gönderilmesi gereken güncelleştirmeleri) belirler.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="3a8e1-202">Özgün değerler, varlık sorgulandığında veya eklendiğinde saklanır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="3a8e1-203">Otomatik değişiklik algılamaya neden olan yöntemlerin bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="3a8e1-204">DbContext. SaveChanges</span><span class="sxs-lookup"><span data-stu-id="3a8e1-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="3a8e1-205">DbContext. Entry</span><span class="sxs-lookup"><span data-stu-id="3a8e1-205">DbContext.Entry</span></span>

* <span data-ttu-id="3a8e1-206">ChangeTracker. Entries</span><span class="sxs-lookup"><span data-stu-id="3a8e1-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="3a8e1-207">Çok sayıda varlığı izliyorsanız ve bu yöntemlerden birini bir döngüde birçok kez çağırırsanız, özelliği kullanarak otomatik değişiklik algılamayı geçici olarak kapatarak önemli performans iyileştirmeleri alabilirsiniz `ChangeTracker.AutoDetectChangesEnabled` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="3a8e1-208">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="3a8e1-209">EF Core kaynak kodu ve geliştirme planları</span><span class="sxs-lookup"><span data-stu-id="3a8e1-209">EF Core source code and development plans</span></span>

<span data-ttu-id="3a8e1-210">Entity Framework Core kaynağı [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="3a8e1-211">EF Core deposu gecelik derlemeler, sorun izleme, özellik özellikleri, tasarım toplantısı notları ve [ileride geliştirmeye yönelik yol haritasını](https://github.com/dotnet/efcore/wiki/Roadmap)içerir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="3a8e1-212">Hataları dosyalayabilirsiniz veya bulabilir ve katkıda bulunabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="3a8e1-213">Kaynak kodu açık olsa da Entity Framework Core, Microsoft ürünü olarak tam olarak desteklenmektedir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="3a8e1-214">Microsoft Entity Framework ekibi, her bir yayının kalitesini sağlamak için, hangi katkıların kabul edildiğini denetler ve tüm kod değişikliklerini sınar.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="3a8e1-215">Mevcut veritabanından ters mühendislik</span><span class="sxs-lookup"><span data-stu-id="3a8e1-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="3a8e1-216">Mevcut bir veritabanından varlık sınıfları dahil bir veri modeline ters mühendislik uygulamak için [Scaffold-DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="3a8e1-217">Bkz. Başlangıç [öğreticisi](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="3a8e1-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="3a8e1-218">Kodu basitleştirmek için dinamik LINQ kullanma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="3a8e1-219">[Bu serideki üçüncü öğretici](sort-filter-page.md) , bir bildirimde sabit kodlayarak sütun adları aracılığıyla LINQ kodunun nasıl yazılacağını gösterir `switch` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="3a8e1-220">Arasından seçim yapabileceğiniz iki sütun varsa, bu sorunsuz bir şekilde yapılır, ancak çok sayıda sütununuzla karşılaşırsanız, kod ayrıntılı alabilir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="3a8e1-221">Bu sorunu çözmek için `EF.Property` yöntemini kullanarak özelliğin adını bir dize olarak belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="3a8e1-222">Bu yaklaşımı denemek için `Index` , içindeki yöntemini `StudentsController` aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="3a8e1-223">Teşekkürler</span><span class="sxs-lookup"><span data-stu-id="3a8e1-223">Acknowledgments</span></span>

<span data-ttu-id="3a8e1-224">Tom Dykstra ve Rick Anderson (Twitter @RickAndMSFT ) bu öğreticiyi yazdı.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="3a8e1-225">ROWA Miller, Diego Vega ve kod incelemeleri ile Entity Framework ekip yardımlı diğer üyeleri ve öğreticiler için kod yazarken oluşan sorunları ayıkladık.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="3a8e1-226">John Parente ve Paul Goldman, 2,2 ASP.NET Core öğreticisini güncelleştirmeye çalıştı.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="3a8e1-227">Sık karşılaşılan hataları giderme</span><span class="sxs-lookup"><span data-stu-id="3a8e1-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="3a8e1-228">Başka bir işlem tarafından kullanılan ContosoUniversity.dll</span><span class="sxs-lookup"><span data-stu-id="3a8e1-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="3a8e1-229">Hata iletisi:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-229">Error message:</span></span>

> <span data-ttu-id="3a8e1-230">' ...bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' yazma için açılamıyor--' başka bir işlem tarafından kullanıldığı için işlem ' ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' dosyasına erişemiyor.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="3a8e1-231">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-231">Solution:</span></span>

<span data-ttu-id="3a8e1-232">IIS Express sitesini durdurun.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="3a8e1-233">Windows sistemi tepsisine IIS Express bulun ve simgesine sağ tıklayın, Contoso Üniversitesi sitesini seçin ve ardından **siteyi durdur** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site** .</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="3a8e1-234">Yukarı ve aşağı metotlarda kod olmadan geçiş yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="3a8e1-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="3a8e1-235">Olası neden:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-235">Possible cause:</span></span>

<span data-ttu-id="3a8e1-236">EF CLı komutları, kod dosyalarını otomatik olarak kapatmaz ve kaydetmez.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="3a8e1-237">Komutu çalıştırdığınızda kaydetmediğiniz değişiklikler varsa `migrations add` , EF değişikliklerinizi bulamaz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="3a8e1-238">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-238">Solution:</span></span>

<span data-ttu-id="3a8e1-239">Komutunu çalıştırın `migrations remove` , kod değişikliklerinizi kaydedin ve komutu yeniden çalıştırın `migrations add` .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="3a8e1-240">Veritabanı güncelleştirmesi çalıştırılırken hatalar oluştu</span><span class="sxs-lookup"><span data-stu-id="3a8e1-240">Errors while running database update</span></span>

<span data-ttu-id="3a8e1-241">Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="3a8e1-242">Çözümleyemez geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="3a8e1-243">Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi çok daha yüksektir.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="3a8e1-244">En basit yaklaşım, içindeki veritabanını yeniden adlandırmalıdır *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="3a8e1-244">The simplest approach is to rename the database in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="3a8e1-245">Bir sonraki sefer çalıştırdığınızda `database update` Yeni bir veritabanı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="3a8e1-246">SSOX 'te bir veritabanını silmek için veritabanına sağ tıklayın, **Sil** ' e tıklayın ve ardından **veritabanını sil** Iletişim kutusunda **varolan bağlantıları kapat** ' ı seçin ve **Tamam** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-246">To delete a database in SSOX, right-click the database, click **Delete** , and then in the **Delete Database** dialog box select **Close existing connections** and click **OK** .</span></span>

<span data-ttu-id="3a8e1-247">CLı kullanarak bir veritabanını silmek için `database drop` CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="3a8e1-248">SQL Server örneği bulunurken hata oluştu</span><span class="sxs-lookup"><span data-stu-id="3a8e1-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="3a8e1-249">Hata İletisi:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-249">Error Message:</span></span>

> <span data-ttu-id="3a8e1-250">SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="3a8e1-251">Sunucu bulunamadı veya erişilebilir değildi.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="3a8e1-252">Örnek adının doğru olduğundan ve SQL Server uzak bağlantılara izin verecek şekilde yapılandırıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="3a8e1-253">(sağlayıcı: SQL ağ arabirimleri, hata: 26-belirtilen sunucu/örnek bulunurken hata oluştu)</span><span class="sxs-lookup"><span data-stu-id="3a8e1-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="3a8e1-254">Çözüm:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-254">Solution:</span></span>

<span data-ttu-id="3a8e1-255">Bağlantı dizesini denetleyin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-255">Check the connection string.</span></span> <span data-ttu-id="3a8e1-256">Veritabanı dosyasını el ile sildiyseniz, oluşturma dizesindeki veritabanının adını yeni bir veritabanı ile başlatılacak şekilde değiştirin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="3a8e1-257">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-257">Get the code</span></span>

[<span data-ttu-id="3a8e1-258">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="3a8e1-259">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3a8e1-259">Additional resources</span></span>

<span data-ttu-id="3a8e1-260">EF Core hakkında daha fazla bilgi için [Entity Framework Core belgelerine](/ef/core)bakın.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="3a8e1-261">Bir kitap da kullanılabilir: [Entity Framework Core eylem](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="3a8e1-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="3a8e1-262">Bir Web uygulamasının nasıl dağıtılacağı hakkında bilgi için bkz <xref:host-and-deploy/index> ..</span><span class="sxs-lookup"><span data-stu-id="3a8e1-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="3a8e1-263">Kimlik doğrulama ve yetkilendirme gibi ASP.NET Core MVC ile ilgili diğer konular hakkında daha fazla bilgi için bkz <xref:index> ..</span><span class="sxs-lookup"><span data-stu-id="3a8e1-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3a8e1-264">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="3a8e1-264">Next steps</span></span>

<span data-ttu-id="3a8e1-265">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="3a8e1-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3a8e1-266">Ham SQL sorguları gerçekleştiriliyor</span><span class="sxs-lookup"><span data-stu-id="3a8e1-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="3a8e1-267">Varlıkları döndürmek için sorgu çağrıldı</span><span class="sxs-lookup"><span data-stu-id="3a8e1-267">Called a query to return entities</span></span>
> * <span data-ttu-id="3a8e1-268">Diğer türleri döndürmek için sorgu çağrıldı</span><span class="sxs-lookup"><span data-stu-id="3a8e1-268">Called a query to return other types</span></span>
> * <span data-ttu-id="3a8e1-269">Bir güncelleştirme sorgusu çağrıldı</span><span class="sxs-lookup"><span data-stu-id="3a8e1-269">Called an update query</span></span>
> * <span data-ttu-id="3a8e1-270">İncelenen SQL sorguları</span><span class="sxs-lookup"><span data-stu-id="3a8e1-270">Examined SQL queries</span></span>
> * <span data-ttu-id="3a8e1-271">Soyutlama katmanı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3a8e1-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="3a8e1-272">Otomatik değişiklik algılama hakkında bilgi edinildi</span><span class="sxs-lookup"><span data-stu-id="3a8e1-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="3a8e1-273">EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinildi</span><span class="sxs-lookup"><span data-stu-id="3a8e1-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="3a8e1-274">Kodu basitleştirmek için dinamik LINQ kullanımı öğrenildi</span><span class="sxs-lookup"><span data-stu-id="3a8e1-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="3a8e1-275">Bu, ASP.NET Core MVC uygulamasında Entity Framework Core kullanımı hakkında bu öğretici serisini tamamlar.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="3a8e1-276">Bu seri yeni bir veritabanıyla çalıştı; bir alternatif, mevcut bir veritabanından bir modele tersine mühendislik kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="3a8e1-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3a8e1-277">Öğretici: MVC ile EF Core, var olan veritabanı</span><span class="sxs-lookup"><span data-stu-id="3a8e1-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
