---
title: 'Öğretici: Sıralama ekleme, filtreleme ve sayfalama - EF Core ile MVC ASP.NET'
description: Bu öğreticide Öğrenci Dizini sayfasına sıralama, filtreleme ve sayfalama işlevleri eklersiniz. Ayrıca, basit gruplandırma yapan bir sayfa da oluşturursunuz.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 99bf9ed59b47e8fbba838b97c3e032b9808f6a94
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657138"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="bd2a0-104">Öğretici: Sıralama ekleme, filtreleme ve sayfalama - EF Core ile MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="bd2a0-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="bd2a0-105">Önceki öğreticide, Öğrenci kuruluşları için temel CRUD işlemleri için bir web sayfası kümesi uygulamışsınız.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="bd2a0-106">Bu öğreticide Öğrenci Dizini sayfasına sıralama, filtreleme ve sayfalama işlevleri eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="bd2a0-107">Ayrıca, basit gruplandırma yapan bir sayfa da oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="bd2a0-108">Aşağıdaki resimde, sayfanız bittiğinde nasıl görüneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="bd2a0-109">Sütun başlıkları, kullanıcının bu sütuna göre sıralamak için tıklatabileceği bağlantılardır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="bd2a0-110">Bir sütun başlığını tıklatmak, artan ve azalan sıralama sırası arasında tekrar tekrar geçiş eder.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="bd2a0-112">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd2a0-113">Sütun sıralama bağlantıları ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-113">Add column sort links</span></span>
> * <span data-ttu-id="bd2a0-114">Arama kutusu ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-114">Add a Search box</span></span>
> * <span data-ttu-id="bd2a0-115">Öğrenci Endeksine Sayfa ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="bd2a0-116">Dizin yöntemine sayfa ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-116">Add paging to Index method</span></span>
> * <span data-ttu-id="bd2a0-117">Sayfalama bağlantıları ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-117">Add paging links</span></span>
> * <span data-ttu-id="bd2a0-118">Hakkında sayfa oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bd2a0-119">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="bd2a0-119">Prerequisites</span></span>

* [<span data-ttu-id="bd2a0-120">CRUD İşlevselliğini Uygulayın</span><span class="sxs-lookup"><span data-stu-id="bd2a0-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="bd2a0-121">Sütun sıralama bağlantıları ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-121">Add column sort links</span></span>

<span data-ttu-id="bd2a0-122">Öğrenci Dizini sayfasına sıralama eklemek için, `Index` Öğrenci denetleyicisinin yöntemini değiştirir ve Öğrenci Dizini görünümüne kod eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="bd2a0-123">Dizin yöntemine sıralama İşlevselliği ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="bd2a0-124">*StudentsController.cs*olarak, `Index` yöntemi aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="bd2a0-125">Bu kod, `sortOrder` URL'deki sorgu dizesinden bir parametre alır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="bd2a0-126">Sorgu dize değeri, ASP.NET Core MVC tarafından eylem yönteminin parametresi olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="bd2a0-127">Parametre, isteğe bağlı olarak bir alt alt alt sayı ve azalan sırayı belirtmek için "desc" dizesi tarafından izlenen "Ad" veya "Tarih" dizesi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="bd2a0-128">Varsayılan sıralama sırası yükseliyor.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-128">The default sort order is ascending.</span></span>

<span data-ttu-id="bd2a0-129">Dizin sayfası ilk kez istendiğinde, sorgu dizesi yoktur.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="bd2a0-130">Öğrenciler, `switch` ekstredeki düşme durumunda belirlenen varsayılan değer olan soyadına göre artan sırada görüntülenirler.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="bd2a0-131">Kullanıcı bir sütun başlığı köprü tıklattığında, `sortOrder` uygun değer sorgu dizesinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="bd2a0-132">İki `ViewData` öğe (NameSortParm ve DateSortParm) uygun sorgu dize değerleri ile sütun başlığı köprüler yapılandırmak için görünüm tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="bd2a0-133">Bunlar üçüncül ifadeler.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-133">These are ternary statements.</span></span> <span data-ttu-id="bd2a0-134">İlkinde, parametre boş `sortOrder` veya boşsa, NameSortParm'ın "name_desc" olarak ayarlanılması gerektiği belirtilir; aksi takdirde, boş bir dize olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="bd2a0-135">Bu iki deyim, görünümün sütun başlığı köprülerini aşağıdaki gibi ayarlamasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="bd2a0-136">Geçerli sıralama sırası</span><span class="sxs-lookup"><span data-stu-id="bd2a0-136">Current sort order</span></span>  | <span data-ttu-id="bd2a0-137">Soyadı Köprü</span><span class="sxs-lookup"><span data-stu-id="bd2a0-137">Last Name Hyperlink</span></span> | <span data-ttu-id="bd2a0-138">Tarih Köprü</span><span class="sxs-lookup"><span data-stu-id="bd2a0-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="bd2a0-139">Artan Soyadı</span><span class="sxs-lookup"><span data-stu-id="bd2a0-139">Last Name ascending</span></span>  | <span data-ttu-id="bd2a0-140">descending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-140">descending</span></span>          | <span data-ttu-id="bd2a0-141">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-141">ascending</span></span>      |
| <span data-ttu-id="bd2a0-142">Soyadı azalan</span><span class="sxs-lookup"><span data-stu-id="bd2a0-142">Last Name descending</span></span> | <span data-ttu-id="bd2a0-143">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-143">ascending</span></span>           | <span data-ttu-id="bd2a0-144">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-144">ascending</span></span>      |
| <span data-ttu-id="bd2a0-145">Artan tarih</span><span class="sxs-lookup"><span data-stu-id="bd2a0-145">Date ascending</span></span>       | <span data-ttu-id="bd2a0-146">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-146">ascending</span></span>           | <span data-ttu-id="bd2a0-147">descending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-147">descending</span></span>     |
| <span data-ttu-id="bd2a0-148">Azalan tarih</span><span class="sxs-lookup"><span data-stu-id="bd2a0-148">Date descending</span></span>      | <span data-ttu-id="bd2a0-149">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-149">ascending</span></span>           | <span data-ttu-id="bd2a0-150">ascending</span><span class="sxs-lookup"><span data-stu-id="bd2a0-150">ascending</span></span>      |

<span data-ttu-id="bd2a0-151">Yöntem, sıralamak için sütun belirtmek için Varlıklar LINQ kullanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="bd2a0-152">Kod, anahtar `IQueryable` deyiminden önce bir değişken oluşturur, anahtar deyiminde değiştirir `ToListAsync` ve `switch` deyiminden sonra yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="bd2a0-153">Değişkenler oluşturup `IQueryable` değiştirdiğinizde, veritabanına sorgu gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="bd2a0-154">Sorgu, `IQueryable` `ToListAsync`nesneyi koleksiyona dönüştürene kadar yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="bd2a0-155">Bu nedenle, bu kod `return View` deyimi kadar yürütülmeyen tek bir sorgu ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="bd2a0-156">Bu kod, çok sayıda sütunla ayrıntılı olabilir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="bd2a0-157">[Bu serinin son öğretici](advanced.md#dynamic-linq) bir `OrderBy` dize değişkeninde sütunun adını geçmenizi sağlayan kod yazmak için nasıl gösterir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="bd2a0-158">Öğrenci Dizini görünümüne sütun başlığı köprüleri ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="bd2a0-159">*Görünümler/Öğrenciler/Index.cshtml'deki*kodu sütun başlığı köprüleri eklemek için aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="bd2a0-160">Değiştirilen satırlar vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="bd2a0-161">Bu kod, uygun `ViewData` sorgu dize değerleri ile köprüler kurmak için özellikleri bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="bd2a0-162">Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin ve sıralamanın işe yaradığını doğrulamak için **Soyadı** ve **Kayıt Tarihi** sütunbaşlıklarını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Ad sırasına göre öğrenci dizini sayfası](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="bd2a0-164">Arama kutusu ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-164">Add a Search box</span></span>

<span data-ttu-id="bd2a0-165">Öğrenci Dizini sayfasına filtreleme eklemek için, görünüme bir metin kutusu ve gönder düğmesi `Index` ekler ve yöntemde ilgili değişiklikler yaparsınız.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="bd2a0-166">Metin kutusu, ad ve soyad alanlarında aramak için bir dize girmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="bd2a0-167">Dizin yöntemine filtreleme işlevi ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="bd2a0-168">*StudentsController.cs,* `Index` yöntemi aşağıdaki kodla değiştirin (değişiklikler vurgulanır).</span><span class="sxs-lookup"><span data-stu-id="bd2a0-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="bd2a0-169">Yönteme bir `searchString` parametre eklediniz. `Index`</span><span class="sxs-lookup"><span data-stu-id="bd2a0-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="bd2a0-170">Arama dizesi değeri, Dizin görünümüne ekleyeceğiniz bir metin kutusundan alınır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="bd2a0-171">Ayrıca LINQ deyimine yalnızca ad veya soyad arama dizesini içeren öğrencileri seçen bir yan tümce eklediniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="bd2a0-172">Nerede yan tümcesinin yürütüldüğü yalnızca aranacak bir değer varsa ekleyen deyim.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="bd2a0-173">Burada bir `IQueryable` nesne `Where` üzerinde yöntemi çağırıyorsunuz ve filtre sunucuda işlenir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="bd2a0-174">Bazı `Where` senaryolarda, yöntemi bellek içi bir koleksiyonda uzantı yöntemi olarak adlandırıyor olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="bd2a0-175">(Örneğin, başvuruyu, bir `_context.Students` EF `DbSet` yerine `IEnumerable` bir koleksiyonu döndüren bir depo yöntemine başvurur şekilde değiştirdiğinizi varsayalım.) Sonuç normalde aynı olurdu ama bazı durumlarda farklı olabilir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="bd2a0-176">Örneğin, yöntemin `Contains` .NET Framework uygulaması varsayılan olarak büyük/küçük harf duyarlı bir karşılaştırma gerçekleştirir, ancak SQL Server'da bu, SQL Server örneğinin harmanlama ayarı tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="bd2a0-177">Bu ayar varsayılan olarak büyük/küçük harf duyarsızı dır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="bd2a0-178">Testi açıkça `ToUpper` büyük/küçük harf duyarsız yapmak için yöntemi arayabilirsiniz: *Where(s => s.LastName.ToUpper(). Içerir(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="bd2a0-179">Bu, kodu daha sonra `IEnumerable` `IQueryable` nesne yerine bir koleksiyon döndüren bir depo kullanmak üzere değiştirirseniz sonuçların aynı kalmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="bd2a0-180">(Bir `Contains` `IEnumerable` koleksiyondaki yöntemi aradiğinizde,.NET Framework uygulamasını alırsınız; bir `IQueryable` nesne üzerinde aradiğinizde veritabanı sağlayıcısı uygulamasını alırsınız.) Ancak, bu çözüm için bir performans cezası var.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="bd2a0-181">Kod, `ToUpper` TSQL SELECT deyiminin WHERE yan tümcesi'ne bir işlev koyar.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="bd2a0-182">Bu, en iyi duruma getiricinin dizin kullanmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="bd2a0-183">SQL'in çoğunlukla büyük/küçük harf duyarlı olarak yüklendiğinden, `ToUpper` büyük/küçük harf duyarlı bir veri deposuna geçiş yapana kadar koddan kaçınmak en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="bd2a0-184">Öğrenci Dizini Görünümüne Arama Kutusu Ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="bd2a0-185">*Görünümler/Öğrenci/Index.cshtml'de,* resim yazısı, metin kutusu ve **Arama** düğmesi oluşturmak için vurgulanan kodu açılış tablosu etiketinden hemen önce ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="bd2a0-186">Bu kod, `<form>` arama metin kutusunu ve düğmesini eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="bd2a0-187">Varsayılan olarak, `<form>` etiket yardımcısı form verilerini BIR POST ile gönderir, bu da parametrelerin SORGU dizeleri olarak URL'de değil, HTTP ileti gövdesinde geçirildiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="bd2a0-188">HTTP GET'i belirttiğiniz zaman, form verileri URL'ye sorgu dizeleri olarak aktarılır ve bu da kullanıcıların URL'yi yer imi ne kadar işaretlemelerine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="bd2a0-189">W3C yönergeleri, eylem bir güncelleştirmeyle sonuçlanmadığında GET'i kullanmanızı önerir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="bd2a0-190">Uygulamayı çalıştırın, **Öğrenciler** sekmesini seçin, bir arama dizesi girin ve filtrelemenin çalıştığını doğrulamak için Ara'yı tıklatın.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Filtreleme ile öğrenci dizini sayfası](sort-filter-page/_static/filtering.png)

<span data-ttu-id="bd2a0-192">URL'nin arama dizesini içerdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="bd2a0-193">Bu sayfayı yer imi ederseniz, yer imi kullandığınızda filtrelenmiş listeyi alırsınız.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="bd2a0-194">Etikete `method="get"` `form` ekleme, sorgu dizesinin oluşturulmasına neden olan şeydir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="bd2a0-195">Bu aşamada, bir sütun başlığı sıralama bağlantısını tıklattığınızda, **Arama** kutusuna girdiğiniz filtre değerini kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="bd2a0-196">Bunu bir sonraki bölümde düzelteceksin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="bd2a0-197">Öğrenci Endeksine Sayfa ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-197">Add paging to Students Index</span></span>

<span data-ttu-id="bd2a0-198">Öğrenci Dizini sayfasına sayfa ekleme eklemek için, tablonun tüm satırlarını her zaman almak yerine sunucudaki verileri filtrelemek için kullanan `PaginatedList` `Skip` ve `Take` ifade veren bir sınıf oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="bd2a0-199">Ardından `Index` yöntemde ek değişiklikler yapar ve görünüme sayfalama `Index` düğmeleri eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="bd2a0-200">Aşağıdaki resimde sayfalama düğmeleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-200">The following illustration shows the paging buttons.</span></span>

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="bd2a0-202">Proje klasöründe, `PaginatedList.cs`şablon kodunu aşağıdaki kodla oluşturun ve değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="bd2a0-203">Bu `CreateAsync` koddaki yöntem sayfa boyutunu ve sayfa numarasını `Skip` `Take` alır ve `IQueryable`uygun ve ifadeleri uygular.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="bd2a0-204">`IQueryable`Çağrıldığında, `ToListAsync` yalnızca istenen sayfayı içeren bir Liste döndürecektir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="bd2a0-205">Özellikleri `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="bd2a0-206">Oluşturucular eşzamanlı kodu `CreateAsync` çalıştıramadığından, `PaginatedList<T>` nesneyi oluşturmak için oluşturucu yerine bir yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="bd2a0-207">Dizin yöntemine sayfa ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-207">Add paging to Index method</span></span>

<span data-ttu-id="bd2a0-208">*StudentsController.cs,* yöntemi `Index` aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="bd2a0-209">Bu kod, yöntem imzasına bir sayfa numarası parametresi, geçerli sıralama sipariş parametresi ve geçerli filtre parametresi ekler.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="bd2a0-210">Sayfa ilk kez görüntülendiğinde veya kullanıcı bir sayfalama veya sıralama bağlantısını tıklatmamışsa, tüm parametreler geçersiz olur.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="bd2a0-211">Bir sayfalama bağlantısı tıklanırsa, sayfa değişkeni görüntülenecek sayfa numarasını içerir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="bd2a0-212">CurrentSort adlı `ViewData` öğe görünümü geçerli sıralama sırasıyla sağlar, çünkü bu, sıralama sırasını çağrıyaparken aynı tutmak için çağrı bağlantılarına dahil edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="bd2a0-213">CurrentFilter adlı `ViewData` öğe, görünümü geçerli filtre dizesiyle sağlar.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="bd2a0-214">Sayfalama sırasında filtre ayarlarını korumak için bu değer sayfalama bağlantılarına eklenmelidir ve sayfa yeniden görüntülendiğinde metin kutusuna geri yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="bd2a0-215">Arama dizesi sayfalama sırasında değiştirilirse, yeni filtre farklı verilerin görüntülenmesine neden olabileceğinden sayfanın 1 olarak sıfırlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="bd2a0-216">Metin kutusuna bir değer girildiğinde ve Gönder düğmesine basıldığında arama dizesi değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="bd2a0-217">Bu durumda, `searchString` parametre null değildir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="bd2a0-218">`Index` Yöntemin sonunda, `PaginatedList.CreateAsync` yöntem öğrenci sorgusunu çağrılamayı destekleyen bir koleksiyon türündeki tek bir öğrenci sayfasına dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="bd2a0-219">Öğrencilerin bu tek sayfası daha sonra görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="bd2a0-220">Yöntem `PaginatedList.CreateAsync` bir sayfa numarası alır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="bd2a0-221">İki soru işareti null-coalescing operatör temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="bd2a0-222">Null-coalescing işleci nullable türü için varsayılan bir değer tanımlar; ifade, `(pageNumber ?? 1)` bir değeri `pageNumber` varsa değerini döndürme veya null `pageNumber` ise 1'i döndürme anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="bd2a0-223">Sayfalama bağlantıları ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-223">Add paging links</span></span>

<span data-ttu-id="bd2a0-224">*Görünümler/Öğrenciler/Index.cshtml'de,* varolan kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="bd2a0-225">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="bd2a0-226">Sayfanın üst kısmındaki deyim, `@model` görünümün artık nesne `PaginatedList<T>` `List<T>` yerine bir nesne aldığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="bd2a0-227">Sütun üstbilgi bağlantıları, kullanıcının filtre sonuçları içinde sıralayabilmeleri için geçerli arama dizesini denetleyiciye geçirmek için sorgu dizesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="bd2a0-228">Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="bd2a0-229">Uygulamayı çalıştırın ve Öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-229">Run the app and go to the Students page.</span></span>

![Sayfalama bağlantıları olan öğrenci dizini sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="bd2a0-231">Sayfalamanın çalıştığından emin olmak için farklı sıralama emirlerindeki çağrı bağlantılarını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="bd2a0-232">Ardından bir arama dizesi girin ve arama nın sıralama ve filtreleme ile de doğru çalıştığını doğrulamak için yeniden aramayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="bd2a0-233">Hakkında sayfa oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-233">Create an About page</span></span>

<span data-ttu-id="bd2a0-234">Contoso Üniversitesi web sitesinin **Hakkında** sayfası için, her kayıt tarihi için kaç öğrencinin kaydolduğunu görüntülersiniz.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="bd2a0-235">Bu, gruplar üzerinde gruplandırma ve basit hesaplamalar gerektirir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="bd2a0-236">Bunu başarmak için aşağıdakileri yaparsınız:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="bd2a0-237">Görünüme geçirmeniz gereken veriler için bir görünüm modeli sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="bd2a0-238">Giriş denetleyicisinde Hakkında yöntemini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="bd2a0-239">About görünümünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="bd2a0-240">Görünüm modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-240">Create the view model</span></span>

<span data-ttu-id="bd2a0-241">*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="bd2a0-242">Yeni klasörde, bir sınıf dosyası *EnrollmentDateGroup.cs* ekleyin ve şablon kodunu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="bd2a0-243">Ev Denetleyicisini Değiştir</span><span class="sxs-lookup"><span data-stu-id="bd2a0-243">Modify the Home Controller</span></span>

<span data-ttu-id="bd2a0-244">*HomeController.cs,* dosyanın üst kısmında aşağıdaki ifadeleri kullanarak ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="bd2a0-245">Sınıf için kıvırcık ayraç açıldıktan hemen sonra veritabanı bağlamı için bir sınıf değişkeni ekleyin ve ASP.NET Core DI'den bağlamın bir örneğini alın:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="bd2a0-246">Aşağıdaki `About` kodla bir yöntem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="bd2a0-247">LINQ deyimi öğrenci varlıklarını kayıt tarihine göre gruplandırıyor, her gruptaki varlık sayısını hesaplar `EnrollmentDateGroup` ve sonuçları görünüm modeli nesneleri koleksiyonunda saklar.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="bd2a0-248">Hakkında Görünüm Oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-248">Create the About View</span></span>

<span data-ttu-id="bd2a0-249">Aşağıdaki kodla *görünümler/ev/about.cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="bd2a0-250">Uygulamayı çalıştırın ve Hakkında sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-250">Run the app and go to the About page.</span></span> <span data-ttu-id="bd2a0-251">Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="bd2a0-252">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-252">Get the code</span></span>

[<span data-ttu-id="bd2a0-253">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="bd2a0-254">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="bd2a0-254">Next steps</span></span>

<span data-ttu-id="bd2a0-255">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="bd2a0-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bd2a0-256">Sütun sıralama bağlantıları eklendi</span><span class="sxs-lookup"><span data-stu-id="bd2a0-256">Added column sort links</span></span>
> * <span data-ttu-id="bd2a0-257">Arama kutusu eklendi</span><span class="sxs-lookup"><span data-stu-id="bd2a0-257">Added a Search box</span></span>
> * <span data-ttu-id="bd2a0-258">Öğrenci Endeksine Çağrı</span><span class="sxs-lookup"><span data-stu-id="bd2a0-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="bd2a0-259">Dizin yöntemine çağrı ekleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-259">Added paging to Index method</span></span>
> * <span data-ttu-id="bd2a0-260">Eklenen sayfalama bağlantıları</span><span class="sxs-lookup"><span data-stu-id="bd2a0-260">Added paging links</span></span>
> * <span data-ttu-id="bd2a0-261">Hakkında sayfa oluşturma</span><span class="sxs-lookup"><span data-stu-id="bd2a0-261">Created an About page</span></span>

<span data-ttu-id="bd2a0-262">Geçişleri kullanarak veri modeli değişikliklerini nasıl işleyeceğinizi öğrenmek için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="bd2a0-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="bd2a0-263">Sonraki: Veri modeli değişikliklerini işleme</span><span class="sxs-lookup"><span data-stu-id="bd2a0-263">Next: Handle data model changes</span></span>](migrations.md)
