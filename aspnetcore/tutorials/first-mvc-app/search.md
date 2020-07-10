---
title: Bölüm 7, ASP.NET Core MVC uygulamasına arama ekleme
author: rick-anderson
description: ASP.NET Core MVC üzerinde öğretici serisinin Bölüm 7.
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 65bf5e6b022699268f9e7e5677ea8632f1a489c7
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213131"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="13442-103">Bölüm 7, ASP.NET Core MVC uygulamasına arama ekleme</span><span class="sxs-lookup"><span data-stu-id="13442-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="13442-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="13442-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="13442-105">Bu bölümde, `Index` film metoduna *tarz* veya *ada*göre arama özelliği ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="13442-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="13442-106">`Index` *Controllers/MoviesController. cs* içinde bulunan yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="13442-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="13442-107">Eylem yönteminin ilk satırı, `Index` filmleri seçmek için bir [LINQ](/dotnet/standard/using-linq) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="13442-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="13442-108">Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**</span><span class="sxs-lookup"><span data-stu-id="13442-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="13442-109">`searchString`Parametresi bir dize içeriyorsa, filmler sorgusu arama dizesinin değerine göre filtrelenecek şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="13442-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="13442-110">`s => s.Title.Contains()`Yukarıdaki kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="13442-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="13442-111">Lambdalar, Yöntem tabanlı [LINQ](/dotnet/standard/using-linq) sorgularında [WHERE](/dotnet/api/system.linq.enumerable.where) yöntemi veya `Contains` (Yukarıdaki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="13442-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="13442-112">LINQ sorguları tanımlandıklarında veya, ya da gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="13442-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="13442-113">Bunun yerine sorgu yürütmesi ertelenir.</span><span class="sxs-lookup"><span data-stu-id="13442-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="13442-114">Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri gerçekten yineleneceği veya yöntem çağrılana kadar geciktirilen anlamına gelir `ToListAsync` .</span><span class="sxs-lookup"><span data-stu-id="13442-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="13442-115">Ertelenmiş sorgu yürütme hakkında daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="13442-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="13442-116">Not: [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi yukarıda gösterilen c# kodunda değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="13442-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="13442-117">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="13442-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="13442-118">SQL Server üzerinde [SQL gibi](/sql/t-sql/language-elements/like-transact-sql)eşlemeler [içerir](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) , büyük/küçük harfe duyarsız olur.</span><span class="sxs-lookup"><span data-stu-id="13442-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="13442-119">SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="13442-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="13442-120">`/Movies/Index` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="13442-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="13442-121">URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` .</span><span class="sxs-lookup"><span data-stu-id="13442-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="13442-122">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="13442-122">The filtered movies are displayed.</span></span>

![Dizin görünümü](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="13442-124">`Index`Yönteminin imzasını adlı bir parametreye sahip olacak şekilde değiştirirseniz `id` , `id` parametresi `{id}` *Startup.cs*içinde ayarlanan varsayılan yollar için isteğe bağlı yer tutucuya eşleşir.</span><span class="sxs-lookup"><span data-stu-id="13442-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="13442-125">Parametresini `id` ve değişikliğin tüm oluşumlarını olarak değiştirin `searchString` `id` .</span><span class="sxs-lookup"><span data-stu-id="13442-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="13442-126">Önceki `Index` Yöntem:</span><span class="sxs-lookup"><span data-stu-id="13442-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="13442-127">`Index`Parametresi ile güncelleştirilmiş Yöntem `id` :</span><span class="sxs-lookup"><span data-stu-id="13442-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="13442-128">Artık arama başlığını sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="13442-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="13442-130">Ancak, kullanıcıların bir filmi her arayışınızda URL 'YI değiştirmesini beklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="13442-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="13442-131">Böylece, filmlerin filtrelemesine yardımcı olmak için UI öğeleri ekleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="13442-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="13442-132">`Index`Yol ile bağlantılı parametrenin nasıl geçirileceğini test etmek için yönteminin imzasını değiştirdiyseniz `ID` , adlı bir parametre alması için geri değiştirin `searchString` :</span><span class="sxs-lookup"><span data-stu-id="13442-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="13442-133">*Views/filmler/Index. cshtml* dosyasını açın ve `<form>` aşağıda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="13442-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="13442-134">HTML etiketi, form `<form>` [etiketi yardımcısını](xref:mvc/views/working-with-forms)kullanır, bu nedenle formu gönderdiğinizde, filtre dizesi `Index` film denetleyicisinin eylemine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="13442-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="13442-135">Değişikliklerinizi kaydedin ve sonra filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="13442-135">Save your changes and then test the filter.</span></span>

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="13442-137">`[HttpPost]` `Index` Bekleneceğiniz gibi metodun aşırı yüklemesi yoktur.</span><span class="sxs-lookup"><span data-stu-id="13442-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="13442-138">Bunun için gerekli değildir, çünkü yöntem uygulamanın durumunu değiştirmediğinden verileri filtrelememeniz yeterlidir.</span><span class="sxs-lookup"><span data-stu-id="13442-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="13442-139">Aşağıdaki `[HttpPost] Index` yöntemi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="13442-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="13442-140">`notUsed`Parametresi, yöntemi için bir aşırı yükleme oluşturmak için kullanılır `Index` .</span><span class="sxs-lookup"><span data-stu-id="13442-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="13442-141">Öğreticide daha sonra konuşacağız.</span><span class="sxs-lookup"><span data-stu-id="13442-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="13442-142">Bu yöntemi eklerseniz, Invoker `[HttpPost] Index` yöntemi yöntemiyle eşleşir ve `[HttpPost] Index` yöntemi aşağıdaki görüntüde gösterildiği gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="13442-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![HttpPost dizininden uygulama yanıtı olan tarayıcı penceresi: hayalet üzerinde filtrele](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="13442-144">Ancak, bu yöntemin bu sürümünü eklemeseniz bile `[HttpPost]` `Index` , tümünün nasıl uygulandığını gösteren bir sınırlama vardır.</span><span class="sxs-lookup"><span data-stu-id="13442-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="13442-145">Belirli bir arama için yer işareti koymak istediğinizi veya aynı film filtrelenmiş listesini görmek için onlara tıklabilecekleri bir bağlantı göndermek istediğinizi düşünün.</span><span class="sxs-lookup"><span data-stu-id="13442-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="13442-146">HTTP POST isteğinin URL 'SI GET isteğinin URL 'siyle (localhost: {PORT}/filmler/dizin) aynı olduğunu fark edin; URL 'de arama bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="13442-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="13442-147">Arama dizesi bilgileri sunucuya [form alanı değeri](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="13442-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="13442-148">Tarayıcı geliştirici araçları veya harika [Fiddler aracının](https://www.telerik.com/fiddler)olduğunu doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="13442-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="13442-149">Aşağıdaki görüntüde Chrome tarayıcı geliştirici araçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="13442-149">The image below shows the Chrome browser Developer tools:</span></span>

![Microsoft Edge 'de Geliştirici Araçları, bir searchString değeri hayalet olan bir istek gövdesini gösteren ağ sekmesi](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="13442-151">Arama parametresini ve [XSRF](xref:security/anti-request-forgery) belirtecini istek gövdesinde görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="13442-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="13442-152">Bu şekilde, önceki öğreticide bahsedildiği gibi, [form etiketi Yardımcısı](xref:mvc/views/working-with-forms) , bir [XSRF](xref:security/anti-request-forgery) Anti-forgery belirteci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="13442-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="13442-153">Verileri değiştiriyoruz, bu nedenle denetleyiciyi denetleyici yönteminde doğrulamamız gerekmiyor.</span><span class="sxs-lookup"><span data-stu-id="13442-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="13442-154">Arama parametresi, URL değil, istek gövdesinde olduğundan, bu arama bilgilerini, yer işareti veya başkalarıyla paylaşmak için yakalayamazsınız.</span><span class="sxs-lookup"><span data-stu-id="13442-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="13442-155">İsteğin, `HTTP GET` *Görünümler/filmler/Index. cshtml* dosyasında bulunması gerektiğini belirterek bunu düzeltemedi.</span><span class="sxs-lookup"><span data-stu-id="13442-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="13442-156">Artık bir arama gönderdiğinizde, URL arama sorgu dizesini içerir.</span><span class="sxs-lookup"><span data-stu-id="13442-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="13442-157">`HttpGet Index`Bir yönteminiz olsa da, arama eylem yöntemine de gidecektir `HttpPost Index` .</span><span class="sxs-lookup"><span data-stu-id="13442-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![URL 'de searchString = hayalet ve döndürülen Filmler, Ghostbusters ve Ghostbusters 'ler 2 olan tarayıcı penceresi, hayalet sözcüğünü içerir](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="13442-159">Aşağıdaki biçimlendirme etiketine olan değişikliği gösterir `form` :</span><span class="sxs-lookup"><span data-stu-id="13442-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="13442-160">Türe göre arama Ekle</span><span class="sxs-lookup"><span data-stu-id="13442-160">Add Search by genre</span></span>

<span data-ttu-id="13442-161">`MovieGenreViewModel` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="13442-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="13442-162">Film tarzı görünüm modeli şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="13442-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="13442-163">Bir film listesi.</span><span class="sxs-lookup"><span data-stu-id="13442-163">A list of movies.</span></span>
* <span data-ttu-id="13442-164">`SelectList`Tarzlar listesini içeren bir.</span><span class="sxs-lookup"><span data-stu-id="13442-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="13442-165">Bu, kullanıcının listeden bir tarz seçmesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="13442-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="13442-166">`MovieGenre`, seçilen tarzı içeren.</span><span class="sxs-lookup"><span data-stu-id="13442-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="13442-167">`SearchString`, kullanıcılar arama metin kutusuna girdiğiniz metni içerir.</span><span class="sxs-lookup"><span data-stu-id="13442-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="13442-168">`Index`İçindeki yöntemini `MoviesController.cs` aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="13442-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="13442-169">Aşağıdaki kod, `LINQ` veritabanından tüm tarzları alan bir sorgudur.</span><span class="sxs-lookup"><span data-stu-id="13442-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="13442-170">`SelectList`Tarzlar ayrı tarzlar yansıtıyor (Select listenizin yinelenen tarzlar olmasını istemiyorum).</span><span class="sxs-lookup"><span data-stu-id="13442-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="13442-171">Kullanıcı öğeyi aradığında arama değeri arama kutusuna tutulur.</span><span class="sxs-lookup"><span data-stu-id="13442-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="13442-172">Tarzı, dizin görünümüne göre ara ekleme</span><span class="sxs-lookup"><span data-stu-id="13442-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="13442-173">`Index.cshtml`Şu şekilde *görünümlerde/filmlerde* bulunan güncelleştirme:</span><span class="sxs-lookup"><span data-stu-id="13442-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="13442-174">Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="13442-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="13442-175">Yukarıdaki kodda, `DisplayNameFor` HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="13442-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="13442-176">Lambda ifadesi değerlendirilmek yerine incelenebileceğinden,, `model` `model.Movies` , veya boş olduğunda bir erişim ihlali almazsınız `model.Movies[0]` `null` .</span><span class="sxs-lookup"><span data-stu-id="13442-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="13442-177">Lambda ifadesi değerlendirildiğinde (örneğin, `@Html.DisplayFor(modelItem => item.Title)` ), modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="13442-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="13442-178">Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin:</span><span class="sxs-lookup"><span data-stu-id="13442-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Sonuçları gösteren tarayıcı penceresihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="13442-180">[Önceki](controller-methods-views.md) 
>  [Sonraki](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="13442-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
