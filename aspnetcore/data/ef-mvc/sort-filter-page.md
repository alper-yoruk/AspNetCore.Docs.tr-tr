---
title: 'Öğretici: EF Core sıralama, filtreleme ve sayfalama-ASP.NET MVC ekleme'
description: Bu öğreticide, öğrenciler dizin sayfasına sıralama, filtreleme ve sayfalama işlevselliği ekleyeceksiniz. Ayrıca basit gruplandırma yapan bir sayfa oluşturacaksınız.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 45aabc644fbeaeaa31d534877ba93cb0611f3f34
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401343"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="d80b2-104">Öğretici: EF Core sıralama, filtreleme ve sayfalama-ASP.NET MVC ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="d80b2-105">Önceki öğreticide, öğrenci varlıkları için temel CRUD işlemleri için bir dizi Web sayfası uyguladık.</span><span class="sxs-lookup"><span data-stu-id="d80b2-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="d80b2-106">Bu öğreticide, öğrenciler dizin sayfasına sıralama, filtreleme ve sayfalama işlevselliği ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="d80b2-107">Ayrıca basit gruplandırma yapan bir sayfa oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="d80b2-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="d80b2-108">Aşağıdaki çizimde, işiniz bittiğinde sayfanın nasıl görüneceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="d80b2-109">Sütun başlıkları, kullanıcının sütuna göre sıralamak için tıkladığı bağlantılardır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="d80b2-110">Sütun başlığına tıklanması artan ve azalan sıralama düzeni arasında sürekli olarak geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="d80b2-112">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="d80b2-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d80b2-113">Sütun sıralama bağlantıları Ekle</span><span class="sxs-lookup"><span data-stu-id="d80b2-113">Add column sort links</span></span>
> * <span data-ttu-id="d80b2-114">Arama kutusu ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-114">Add a Search box</span></span>
> * <span data-ttu-id="d80b2-115">Öğrenciler dizinine sayfalama ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="d80b2-116">Dizin yöntemine sayfalama ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-116">Add paging to Index method</span></span>
> * <span data-ttu-id="d80b2-117">Sayfalama bağlantıları Ekle</span><span class="sxs-lookup"><span data-stu-id="d80b2-117">Add paging links</span></span>
> * <span data-ttu-id="d80b2-118">Hakkında bir sayfa oluşturun</span><span class="sxs-lookup"><span data-stu-id="d80b2-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d80b2-119">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="d80b2-119">Prerequisites</span></span>

* [<span data-ttu-id="d80b2-120">CRUD Işlevlerini uygulama</span><span class="sxs-lookup"><span data-stu-id="d80b2-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="d80b2-121">Sütun sıralama bağlantıları Ekle</span><span class="sxs-lookup"><span data-stu-id="d80b2-121">Add column sort links</span></span>

<span data-ttu-id="d80b2-122">Öğrenci dizini sayfasına sıralama eklemek için, `Index` öğrenciler denetleyicisinin yöntemini değiştirecek ve öğrenci dizini görünümüne kod ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="d80b2-123">Dizin yöntemine sıralama Işlevi ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="d80b2-124">*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d80b2-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="d80b2-125">Bu kod `sortOrder` , URL 'deki sorgu dizesinden bir parametre alır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="d80b2-126">Sorgu dizesi değeri, ASP.NET Core MVC tarafından eylem yöntemine bir parametre olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="d80b2-127">Parametresi, "Name" veya "Date" adlı bir dize, isteğe bağlı olarak bir alt çizgi ve azalan sıralama belirtmek için "desc" dizesi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="d80b2-128">Varsayılan sıralama düzeni artan.</span><span class="sxs-lookup"><span data-stu-id="d80b2-128">The default sort order is ascending.</span></span>

<span data-ttu-id="d80b2-129">Dizin sayfası ilk kez istendiğinde sorgu dizesi yoktur.</span><span class="sxs-lookup"><span data-stu-id="d80b2-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="d80b2-130">Öğrenciler, son ada göre artan sırada görüntülenir, bu, deyimdeki en karşı durum ile oluşturulan varsayılandır `switch` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="d80b2-131">Kullanıcı bir sütun başlığı köprüsüne tıkladığında, `sortOrder` sorgu dizesinde uygun değer sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="d80b2-132">İki `ViewData` öğe (Namesortpara ve Datesortpard), sütun başlığı köprülerini uygun sorgu dizesi değerleriyle yapılandırmak için görünüm tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="d80b2-133">Bunlar Üçlü ifadelerdir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-133">These are ternary statements.</span></span> <span data-ttu-id="d80b2-134">Birincisi, `sortOrder` parametre null veya boş ise, Namesortparı 'nin "name_desc" olarak ayarlanması gerektiğini belirtir; Aksi takdirde, boş bir dize olarak ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="d80b2-135">Bu iki deyim, görünümün sütun başlığı köprülerini şu şekilde ayarlamaya olanak tanır:</span><span class="sxs-lookup"><span data-stu-id="d80b2-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="d80b2-136">Geçerli sıralama düzeni</span><span class="sxs-lookup"><span data-stu-id="d80b2-136">Current sort order</span></span>  | <span data-ttu-id="d80b2-137">Son ad Köprüsü</span><span class="sxs-lookup"><span data-stu-id="d80b2-137">Last Name Hyperlink</span></span> | <span data-ttu-id="d80b2-138">Tarih Köprüsü</span><span class="sxs-lookup"><span data-stu-id="d80b2-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="d80b2-139">Artan son ad</span><span class="sxs-lookup"><span data-stu-id="d80b2-139">Last Name ascending</span></span>  | <span data-ttu-id="d80b2-140">descending</span><span class="sxs-lookup"><span data-stu-id="d80b2-140">descending</span></span>          | <span data-ttu-id="d80b2-141">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-141">ascending</span></span>      |
| <span data-ttu-id="d80b2-142">Azalan son ad</span><span class="sxs-lookup"><span data-stu-id="d80b2-142">Last Name descending</span></span> | <span data-ttu-id="d80b2-143">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-143">ascending</span></span>           | <span data-ttu-id="d80b2-144">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-144">ascending</span></span>      |
| <span data-ttu-id="d80b2-145">Artan Tarih</span><span class="sxs-lookup"><span data-stu-id="d80b2-145">Date ascending</span></span>       | <span data-ttu-id="d80b2-146">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-146">ascending</span></span>           | <span data-ttu-id="d80b2-147">descending</span><span class="sxs-lookup"><span data-stu-id="d80b2-147">descending</span></span>     |
| <span data-ttu-id="d80b2-148">Azalan Tarih</span><span class="sxs-lookup"><span data-stu-id="d80b2-148">Date descending</span></span>      | <span data-ttu-id="d80b2-149">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-149">ascending</span></span>           | <span data-ttu-id="d80b2-150">ascending</span><span class="sxs-lookup"><span data-stu-id="d80b2-150">ascending</span></span>      |

<span data-ttu-id="d80b2-151">Yöntemi, sıralama yapılacak sütunu belirtmek için LINQ to Entities kullanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="d80b2-152">Kod, `IQueryable` Switch ifadesinden önce bir değişken oluşturur, Switch ifadesinde değiştirir ve `ToListAsync` deyimden sonra yöntemi çağırır `switch` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="d80b2-153">Değişkenleri oluşturup değiştirirken `IQueryable` veritabanına hiçbir sorgu gönderilmez.</span><span class="sxs-lookup"><span data-stu-id="d80b2-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="d80b2-154">Sorgu, `IQueryable` gibi bir yöntemi çağırarak nesne bir koleksiyona dönüştürülene kadar yürütülmez `ToListAsync` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="d80b2-155">Bu nedenle, bu kod, ifadeye kadar Yürütülmeyen tek bir sorgu ile sonuçlanır `return View` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="d80b2-156">Bu kod çok sayıda sütunla ayrıntı alabilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="d80b2-157">[Bu serideki son öğretici,](advanced.md#dynamic-linq) `OrderBy` bir dize değişkeninde sütunun adını geçirmenize olanak sağlayan kodun nasıl yazılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="d80b2-158">Öğrenci dizini görünümüne sütun başlığı köprüleri ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="d80b2-159">*Görünümler/öğrenciler/Index. cshtml*içindeki kodu, sütun başlığı köprüleri eklemek için aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="d80b2-160">Değiştirilen çizgiler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="d80b2-161">Bu kod, `ViewData` uygun sorgu dizesi değerleriyle köprüler ayarlamak için özelliklerindeki bilgileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="d80b2-162">Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin ve sıralamanın çalıştığını doğrulamak Için **son ad** ve **kayıt tarihi** sütun başlıklarına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d80b2-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Ad düzeninde öğrenciler Dizin sayfası](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="d80b2-164">Arama kutusu ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-164">Add a Search box</span></span>

<span data-ttu-id="d80b2-165">Öğrenciler dizin sayfasına filtre eklemek için, görünüme bir metin kutusu ve Gönder düğmesi ekleyeceksiniz ve yöntemde ilgili değişiklikleri yapmanız gerekir `Index` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="d80b2-166">Metin kutusu, ilk adı ve soyadı alanlarını aramak için bir dize girmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="d80b2-167">Dizin yöntemine filtreleme işlevi ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="d80b2-168">*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin (değişiklikler vurgulanır).</span><span class="sxs-lookup"><span data-stu-id="d80b2-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="d80b2-169">Yöntemine bir parametre eklediniz `searchString` `Index` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="d80b2-170">Arama dizesi değeri, dizin görünümüne ekleyeceğiniz bir metin kutusundan alınır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="d80b2-171">Ayrıca, yalnızca adı veya soyadı arama dizesini içeren öğrencileri seçen bir where yan tümcesine LINQ deyimi de eklediniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="d80b2-172">WHERE yan tümcesini ekleyen deyimi, yalnızca aranacak bir değer varsa yürütülür.</span><span class="sxs-lookup"><span data-stu-id="d80b2-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="d80b2-173">Burada `Where` yöntemi bir nesne üzerinde arıyorsanız `IQueryable` ve filtrenin sunucuda işlenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="d80b2-174">Bazı senaryolarda, `Where` bir bellek içi koleksiyonda yöntemi bir genişletme yöntemi olarak çağırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="d80b2-175">(Örneğin, başvurusunu, `_context.Students` `DbSet` bir koleksiyon döndüren bir depo yöntemine başvurduğu bir değer yerine, olarak değiştirdiğinizi varsayın `IEnumerable` .) Sonuç normalde aynı olur, ancak bazı durumlarda farklı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="d80b2-176">Örneğin, yöntemi .NET Framework uygulama `Contains` Varsayılan olarak büyük/küçük harfe duyarlı bir karşılaştırma gerçekleştirir, ancak SQL Server bu, SQL Server örneğinin harmanlama ayarı tarafından belirlenir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="d80b2-177">Bu ayar varsayılan olarak büyük/küçük harfe duyarsız olur.</span><span class="sxs-lookup"><span data-stu-id="d80b2-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="d80b2-178">`ToUpper`Testi açık büyük/küçük harfe duyarsız yapmak için yöntemini çağırabilirsiniz: *burada (s => s. LastName. ToUpper (). Contains (searchString. ToUpper ())*.</span><span class="sxs-lookup"><span data-stu-id="d80b2-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="d80b2-179">Bu, kodu daha sonra bir nesne yerine bir koleksiyon döndüren depoyu kullanacak şekilde değiştirirseniz sonuçların aynı kalmasını sağlar `IEnumerable` `IQueryable` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="d80b2-180">( `Contains` Bir koleksiyonda yöntemini çağırdığınızda `IEnumerable` .NET Framework uygulamasını alırsınız; bir nesne üzerinde çağırdığınızda `IQueryable` , veritabanı sağlayıcısı uygulamasını alırsınız.) Ancak, bu çözüm için bir performans cezası vardır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="d80b2-181">`ToUpper`Kod, TSQL Select IFADESININ WHERE yan tümcesine bir işlev koyar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="d80b2-182">Bu, iyileştiricinin bir dizin kullanmasını engelleyecek.</span><span class="sxs-lookup"><span data-stu-id="d80b2-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="d80b2-183">SQL 'in çoğu büyük küçük harfe duyarsız olarak yüklendiği için, `ToUpper` büyük/küçük harfe duyarlı bir veri deposuna geçiş yapılıncaya kadar koddan kaçınmak en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="d80b2-184">Öğrenci dizini görünümüne arama kutusu ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="d80b2-185">*Görünümler/öğrenci/Index. cshtml*'de, bir başlık, metin kutusu ve bir **arama** düğmesi oluşturmak için, açılan tablo etiketinden hemen önce vurgulanan kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="d80b2-186">Bu kod, `<form>` Arama metin kutusu ve düğme eklemek için [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) kullanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="d80b2-187">Varsayılan olarak, `<form>` etiket Yardımcısı form verilerini BIR gönderiyle gönderir, yani Parametreler, URL 'de sorgu dizeleri olarak DEĞIL http ileti gövdesinde geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="d80b2-188">HTTP GET belirttiğinizde, form verileri URL 'ye sorgu dizeleri olarak geçirilir ve bu da kullanıcıların URL 'ye yer işareti eklemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="d80b2-189">W3C yönergeleri, eylem bir güncelleştirme ile sonuçlanmazsa Al ' ın kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="d80b2-190">Uygulamayı çalıştırın, **öğrenciler** sekmesini seçin, bir arama dizesi girin ve filtreleme 'nin çalıştığını doğrulamak için ara ' ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d80b2-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Bir filtreleme ile öğrenciler Dizin sayfası](sort-filter-page/_static/filtering.png)

<span data-ttu-id="d80b2-192">URL 'nin arama dizesini içerdiğine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="d80b2-193">Bu sayfaya yer işareti eklerseniz, yer işaretini kullandığınızda filtrelenmiş listeyi alırsınız.</span><span class="sxs-lookup"><span data-stu-id="d80b2-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="d80b2-194">`method="get"` `form` Etiketine ekleme sorgu dizesinin oluşturulmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="d80b2-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="d80b2-195">Bu aşamada, bir sütun başlığı sıralama bağlantısını tıklatırsanız, **arama** kutusuna girdiğiniz filtre değerini kaybedersiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="d80b2-196">Sonraki bölümde bunu çözeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="d80b2-197">Öğrenciler dizinine sayfalama ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-197">Add paging to Students Index</span></span>

<span data-ttu-id="d80b2-198">Öğrenciler dizin sayfasına sayfalama eklemek için, `PaginatedList` `Skip` ve deyimlerini kullanarak, `Take` tablodaki verileri her zaman almak yerine, bu verileri filtrelemek için ve deyimleri kullanan bir sınıf oluşturacaksınız.</span><span class="sxs-lookup"><span data-stu-id="d80b2-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="d80b2-199">Daha sonra, yöntemde ek değişiklikler yapar `Index` ve görünüme sayfalama düğmeleri eklersiniz `Index` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="d80b2-200">Aşağıdaki çizimde sayfalama düğmeleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-200">The following illustration shows the paging buttons.</span></span>

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="d80b2-202">Proje klasöründe, `PaginatedList.cs` şablon kodunu oluşturun ve aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="d80b2-203">`CreateAsync`Bu koddaki yöntemi sayfa boyutunu ve sayfa numarasını alır ve ' a uygun `Skip` ve deyimlerini uygular `Take` `IQueryable` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="d80b2-204">`ToListAsync`Üzerinde çağrıldığında `IQueryable` , yalnızca istenen sayfayı Içeren bir liste döndürür.</span><span class="sxs-lookup"><span data-stu-id="d80b2-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="d80b2-205">Özellikler `HasPreviousPage` ve `HasNextPage` **önceki** ve **sonraki** sayfalama düğmelerini etkinleştirmek veya devre dışı bırakmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="d80b2-206">`CreateAsync` `PaginatedList<T>` Oluşturucular zaman uyumsuz kod çalıştıramadığından, nesneyi oluşturmak için bir Oluşturucu yerine bir yöntem kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="d80b2-207">Dizin yöntemine sayfalama ekleme</span><span class="sxs-lookup"><span data-stu-id="d80b2-207">Add paging to Index method</span></span>

<span data-ttu-id="d80b2-208">*StudentsController.cs*içinde, `Index` yöntemini aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="d80b2-209">Bu kod, bir sayfa numarası parametresi, geçerli bir sıralama düzeni parametresi ve Yöntem imzasına geçerli bir filtre parametresi ekler.</span><span class="sxs-lookup"><span data-stu-id="d80b2-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="d80b2-210">Sayfa ilk kez görüntülenirken veya Kullanıcı bir sayfalama veya sıralama bağlantısına tıklamamışsa, tüm parametreler null olur.</span><span class="sxs-lookup"><span data-stu-id="d80b2-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="d80b2-211">Bir sayfalama bağlantısına tıklandıysanız, sayfa değişkeni görüntülenecek sayfa numarasını içerir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="d80b2-212">`ViewData`Currentsort adlı öğe, sayfalama bağlantılarına dahil edilmesi gerektiğinden, bu sıralama düzeni geçerli sıralama düzeni ile birlikte görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="d80b2-213">`ViewData`Currentfilter adlı öğe, geçerli filtre dizesiyle görünüm sağlar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="d80b2-214">Bu değer, disk belleği sırasında filtre ayarlarını korumak için disk belleği bağlantılarına dahil olmalıdır ve sayfa yeniden görüntülenirken metin kutusuna geri yüklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="d80b2-215">Arama dizesi sayfalama sırasında değiştirilmişse, yeni filtre farklı verilerin görüntülenmesini sağladığından sayfanın 1 olarak sıfırlanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="d80b2-216">Metin kutusuna bir değer girildiğinde ve Gönder düğmesine basıldığında arama dizesi değişir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="d80b2-217">Bu durumda, `searchString` parametre null değil.</span><span class="sxs-lookup"><span data-stu-id="d80b2-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="d80b2-218">`Index`Yöntemin sonunda, `PaginatedList.CreateAsync` yöntemi öğrenci sorgusunu, sayfalama destekleyen bir koleksiyon türünde tek bir öğrenciye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="d80b2-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="d80b2-219">Bu tek bir öğrenci sayfası daha sonra görünüme geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="d80b2-220">`PaginatedList.CreateAsync`Yöntemi bir sayfa numarası alır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="d80b2-221">İki soru işareti, null birleşim işlecini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d80b2-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="d80b2-222">Null birleşim işleci, Nullable bir tür için varsayılan değeri tanımlar; ifade `(pageNumber ?? 1)` `pageNumber` bir değer içeriyorsa değerini döndürür veya null ise 1 döndürür `pageNumber` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="d80b2-223">Sayfalama bağlantıları Ekle</span><span class="sxs-lookup"><span data-stu-id="d80b2-223">Add paging links</span></span>

<span data-ttu-id="d80b2-224">*Görünümler/öğrenciler/Index. cshtml*'de, mevcut kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="d80b2-225">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="d80b2-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="d80b2-226">`@model`Sayfanın üst kısmındaki ifade, görünümün artık nesne yerine bir nesne aldığından emin olarak belirtir `PaginatedList<T>` `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="d80b2-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="d80b2-227">Sütun üst bilgisi bağlantıları, kullanıcının filtre sonuçları içinde sıralama yapabilmesi için geçerli arama dizesini denetleyiciye geçirmek için sorgu dizesini kullanır:</span><span class="sxs-lookup"><span data-stu-id="d80b2-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="d80b2-228">Sayfalama düğmeleri etiket yardımcıları tarafından görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d80b2-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="d80b2-229">Uygulamayı çalıştırın ve öğrenciler sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-229">Run the app and go to the Students page.</span></span>

![Sayfa bağlantılarıyla öğrenciler Dizin sayfası](sort-filter-page/_static/paging.png)

<span data-ttu-id="d80b2-231">Disk belleğinin çalıştığından emin olmak için farklı sıralama emirlerindeki disk belleği bağlantılarına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d80b2-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="d80b2-232">Daha sonra bir arama dizesi girin ve sayfalama ve filtreleme ile doğru şekilde çalıştığını doğrulamak için sayfalama işlemi yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="d80b2-233">Hakkında bir sayfa oluşturun</span><span class="sxs-lookup"><span data-stu-id="d80b2-233">Create an About page</span></span>

<span data-ttu-id="d80b2-234">Contoso Üniversitesi web sitesinin **hakkında** sayfasında, her bir kayıt tarihi için kaç öğrenciye kaydolduğunu görüntüleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d80b2-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="d80b2-235">Bu, gruplar üzerinde gruplandırma ve basit hesaplamalar gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="d80b2-236">Bunu gerçekleştirmek için aşağıdakileri yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="d80b2-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="d80b2-237">Görünüme geçirmeniz gereken veriler için bir görünüm modeli sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d80b2-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="d80b2-238">Giriş denetleyicisinde hakkında yöntemini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d80b2-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="d80b2-239">Hakkında görünümünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d80b2-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="d80b2-240">Görünüm modeli oluşturma</span><span class="sxs-lookup"><span data-stu-id="d80b2-240">Create the view model</span></span>

<span data-ttu-id="d80b2-241">*Modeller* klasöründe bir *SchoolViewModels* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d80b2-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="d80b2-242">Yeni klasörde, *EnrollmentDateGroup.cs* bir sınıf dosyası ekleyin ve şablon kodunu şu kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d80b2-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="d80b2-243">Ana denetleyiciyi değiştirme</span><span class="sxs-lookup"><span data-stu-id="d80b2-243">Modify the Home Controller</span></span>

<span data-ttu-id="d80b2-244">*HomeController.cs*' de, aşağıdaki using deyimlerini dosyanın en üstüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d80b2-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="d80b2-245">Sınıf için açma küme ayracından hemen sonra veritabanı bağlamı için bir sınıf değişkeni ekleyin ve ASP.NET Core DI 'den bağlamın bir örneğini alın:</span><span class="sxs-lookup"><span data-stu-id="d80b2-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="d80b2-246">`About`Aşağıdaki kodla bir yöntem ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d80b2-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="d80b2-247">LINQ beyanı, öğrenci varlıklarını kayıt tarihine göre gruplandırır, her bir gruptaki varlıkların sayısını hesaplar ve sonuçları bir `EnrollmentDateGroup` görünüm modeli nesneleri koleksiyonunda depolar.</span><span class="sxs-lookup"><span data-stu-id="d80b2-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="d80b2-248">Hakkında görünüm oluştur</span><span class="sxs-lookup"><span data-stu-id="d80b2-248">Create the About View</span></span>

<span data-ttu-id="d80b2-249">Aşağıdaki kodla bir *Görünümler/Home/about. cshtml* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d80b2-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="d80b2-250">Uygulamayı çalıştırın ve hakkında sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-250">Run the app and go to the About page.</span></span> <span data-ttu-id="d80b2-251">Her kayıt tarihi için öğrenci sayısı bir tabloda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d80b2-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="d80b2-252">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="d80b2-252">Get the code</span></span>

[<span data-ttu-id="d80b2-253">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="d80b2-254">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="d80b2-254">Next steps</span></span>

<span data-ttu-id="d80b2-255">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="d80b2-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d80b2-256">Sütun sıralama bağlantıları eklendi</span><span class="sxs-lookup"><span data-stu-id="d80b2-256">Added column sort links</span></span>
> * <span data-ttu-id="d80b2-257">Arama kutusu eklendi</span><span class="sxs-lookup"><span data-stu-id="d80b2-257">Added a Search box</span></span>
> * <span data-ttu-id="d80b2-258">Öğrenciler dizinine sayfalama eklendi</span><span class="sxs-lookup"><span data-stu-id="d80b2-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="d80b2-259">Dizin oluşturma yöntemine sayfalama eklendi</span><span class="sxs-lookup"><span data-stu-id="d80b2-259">Added paging to Index method</span></span>
> * <span data-ttu-id="d80b2-260">Sayfalama bağlantıları eklendi</span><span class="sxs-lookup"><span data-stu-id="d80b2-260">Added paging links</span></span>
> * <span data-ttu-id="d80b2-261">Hakkında bir sayfa oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="d80b2-261">Created an About page</span></span>

<span data-ttu-id="d80b2-262">Geçiş kullanarak veri modeli değişikliklerini nasıl işleyebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="d80b2-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d80b2-263">Sonraki: veri modeli değişikliklerini Işle</span><span class="sxs-lookup"><span data-stu-id="d80b2-263">Next: Handle data model changes</span></span>](migrations.md)
