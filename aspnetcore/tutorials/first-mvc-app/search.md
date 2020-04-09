---
title: ASP.NET Core MVC uygulamasına arama ekleme
author: rick-anderson
description: Temel bir ASP.NET Core MVC uygulamasına nasıl arama ekleyeceğinizi gösterir
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662871"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f01ec-103">ASP.NET Core MVC uygulamasına arama ekleme</span><span class="sxs-lookup"><span data-stu-id="f01ec-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f01ec-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f01ec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f01ec-105">Bu bölümde, *türveya* *ada*göre film arama yapmanızı sağlayan eylem yöntemine `Index` arama yeteneği eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="f01ec-106">`Index` *Denetleyiciler/MoviesController.cs* içinde bulunan yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="f01ec-107">`Index` Eylem yönteminin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/standard/using-linq) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f01ec-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f01ec-108">Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**</span><span class="sxs-lookup"><span data-stu-id="f01ec-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f01ec-109">`searchString` Parametre bir dize içeriyorsa, film sorgusu arama dizesinin değerini filtrelemek için değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="f01ec-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="f01ec-110">Yukarıdaki `s => s.Title.Contains()` kod [lambda ifadesidir.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)</span><span class="sxs-lookup"><span data-stu-id="f01ec-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f01ec-111">Lambdas, yöntem tabanlı [LINQ](/dotnet/standard/using-linq) sorgularında [Where](/dotnet/api/system.linq.enumerable.where) metodu veya `Contains` (yukarıdaki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="f01ec-112">LINQ sorguları tanımlandıkları nda veya değiştirildiğinde `Where`, yani . `Contains` `OrderBy`</span><span class="sxs-lookup"><span data-stu-id="f01ec-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="f01ec-113">Bunun yerine, sorgu yürütme ertelendi.</span><span class="sxs-lookup"><span data-stu-id="f01ec-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="f01ec-114">Bu, bir ifadenin değerlendirilmesinin, gerçekleşen değeri gerçekten üzerinde yinelenene veya yöntem çağrılına `ToListAsync` kadar geciktiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f01ec-115">Ertelenmiş sorgu yürütmesi hakkında daha fazla bilgi için sorgu [yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f01ec-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="f01ec-116">Not: [İçerme](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi, yukarıda gösterilen c# kodunda değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="f01ec-117">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f01ec-118">SQL Server'da, sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)için haritalar [içerir,](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) bu da büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f01ec-119">SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.</span><span class="sxs-lookup"><span data-stu-id="f01ec-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f01ec-120">`/Movies/Index` sayfasına gidin.</span><span class="sxs-lookup"><span data-stu-id="f01ec-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="f01ec-121">URL gibi `?searchString=Ghost` bir sorgu dizesini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f01ec-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="f01ec-122">Filtre uygulanmış filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-122">The filtered movies are displayed.</span></span>

![Dizin görünümü](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="f01ec-124">Yöntemin `Index` imzasını bir parametre olarak `id`değiştirirseniz, `id` parametre *Startup.cs* `{id}` ayarlanan varsayılan yollar için isteğe bağlı yer tutucuyla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="f01ec-125">Parametreyi `id` ve tüm `searchString` değişiklik oluşumlarını `id`' ya çevirin.</span><span class="sxs-lookup"><span data-stu-id="f01ec-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="f01ec-126">Önceki `Index` yöntem:</span><span class="sxs-lookup"><span data-stu-id="f01ec-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="f01ec-127">Parametre `Index` ile `id` güncelleştirilmiş yöntem:</span><span class="sxs-lookup"><span data-stu-id="f01ec-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="f01ec-128">Artık arama başlığını sorgu dizesi değeri yerine rota verisi (URL segmenti) olarak geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="f01ec-130">Ancak, kullanıcıların bir film aramak istediklerinde URL'yi değiştirmelerini bekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="f01ec-131">Şimdi filmleri filtrelemelerine yardımcı olmak için Ara Birimi öğelerini eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="f01ec-132">Rotaya bağlı `Index` `ID` parametrenin nasıl geçeceğini test etmek için yöntemin imzasını değiştirdiyseniz, adı `searchString`verilen bir parametre yi kapacak şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="f01ec-133">*Görünümler/Filmler/Index.cshtml* dosyasını açın `<form>` ve aşağıda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="f01ec-134">HTML `<form>` etiketi [Form Tag Helper](xref:mvc/views/working-with-forms)kullanır, böylece formu gönderdiğinde, filtre `Index` dizesi film denetleyicisinin eylemine nakledilir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="f01ec-135">Değişikliklerinizi kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="f01ec-135">Save your changes and then test the filter.</span></span>

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="f01ec-137">Tahmin edebileceğiniz `[HttpPost]` gibi `Index` yöntemin aşırı yüklenmez.</span><span class="sxs-lookup"><span data-stu-id="f01ec-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="f01ec-138">Yöntem uygulamanın durumunu değiştirmediği için buna ihtiyacınız yok, yalnızca verileri filtreleme.</span><span class="sxs-lookup"><span data-stu-id="f01ec-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="f01ec-139">Aşağıdaki `[HttpPost] Index` yöntemi ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="f01ec-140">`notUsed` Parametre `Index` yöntem için bir aşırı yük oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="f01ec-141">Bunu daha sonra öğreticide konuşuruz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="f01ec-142">Bu yöntemi eklerseniz, eylem çağıran `[HttpPost] Index` yöntem eşleşir `[HttpPost] Index` ve yöntem aşağıdaki resimde gösterildiği gibi çalışır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![HttpPost Index uygulama yanıtı ile Tarayıcı penceresi: hayalet filtre](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="f01ec-144">Ancak, `Index` yöntemin bu `[HttpPost]` sürümünü ekleseniz bile, tüm bunların nasıl uygulandığı konusunda bir sınırlama vardır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="f01ec-145">Belirli bir aramayı yer imi yapmak istediğinizi veya aynı filtreuygulanmış film listesini görmek için tıklatabilecekleri arkadaşlarınıza bir bağlantı göndermek istediğinizi düşünün.</span><span class="sxs-lookup"><span data-stu-id="f01ec-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="f01ec-146">HTTP POST isteğinin URL'sinin GET isteğinin URL'si ile aynı olduğuna dikkat edin (localhost:{PORT}/Filmler/Dizini) - URL'de arama bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="f01ec-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="f01ec-147">Arama dizesi bilgileri [bir form alanı değeri](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)olarak sunucuya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="f01ec-148">Bunu tarayıcı Geliştirici araçları veya mükemmel [Fiddler aracı](https://www.telerik.com/fiddler)ile doğrulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="f01ec-149">Aşağıdaki resimde Chrome tarayıcı geliştirici araçları gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="f01ec-149">The image below shows the Chrome browser Developer tools:</span></span>

![Microsoft Edge'deki Geliştirici Araçları'nın ağ sekmesi, bir istek gövdesini hayaletin searchString değerine sahip gösteren](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="f01ec-151">İstek gövdesinde arama parametresini ve [XSRF](xref:security/anti-request-forgery) belirtecinizi görebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f01ec-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="f01ec-152">Not, önceki öğreticide belirtildiği gibi, [Form Tag Helper](xref:mvc/views/working-with-forms) bir [XSRF](xref:security/anti-request-forgery) anti-sahtebelirteci oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f01ec-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="f01ec-153">Verileri değiştirmiyoruz, bu nedenle denetleyici yönteminde belirteçleri doğrulamamız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f01ec-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="f01ec-154">Arama parametresi URL'de değil, istek gövdesinde olduğundan, bu arama bilgilerini yer imi yapmak veya başkalarıyla paylaşmak için yakalayamadığınıziçin.</span><span class="sxs-lookup"><span data-stu-id="f01ec-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="f01ec-155">İstek belirterek düzeltin `HTTP GET` *Görünümler/Filmler/Index.cshtml* dosyasında bulunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="f01ec-156">Şimdi bir arama gönderdiğinde, URL arama sorgu dizesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="f01ec-157">Arama, bir `HttpGet Index` `HttpPost Index` yönteminiz olsa bile eylem yöntemine de gider.</span><span class="sxs-lookup"><span data-stu-id="f01ec-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Tarayıcı penceresi aramaString = Url hayalet gösteren ve filmler döndü, Ghostbusters ve Ghostbusters 2, kelime hayalet içeren](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="f01ec-159">Aşağıdaki biçimlendirme `form` etiketteki değişikliği gösterir:</span><span class="sxs-lookup"><span data-stu-id="f01ec-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="f01ec-160">Türe göre Arama Ekle</span><span class="sxs-lookup"><span data-stu-id="f01ec-160">Add Search by genre</span></span>

<span data-ttu-id="f01ec-161">`MovieGenreViewModel` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="f01ec-162">Film türü görünümü modeli şunları içerecektir:</span><span class="sxs-lookup"><span data-stu-id="f01ec-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="f01ec-163">Filmlerin listesi.</span><span class="sxs-lookup"><span data-stu-id="f01ec-163">A list of movies.</span></span>
* <span data-ttu-id="f01ec-164">Türlerin `SelectList` listesini içeren bir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="f01ec-165">Bu, kullanıcının listeden bir tür seçmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f01ec-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="f01ec-166">`MovieGenre`, seçili türü içerir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="f01ec-167">`SearchString`, kullanıcıların arama metin kutusuna girdikleri metni içerir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="f01ec-168">Yöntemi `Index` aşağıdaki `MoviesController.cs` kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f01ec-169">Aşağıdaki kod, `LINQ` veritabanından tüm türler alır bir sorgudur.</span><span class="sxs-lookup"><span data-stu-id="f01ec-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="f01ec-170">Türler, `SelectList` farklı türler yansıtılarak oluşturulur (seçili listemizin yinelenen türlere sahip olmasını istemeyiz).</span><span class="sxs-lookup"><span data-stu-id="f01ec-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="f01ec-171">Kullanıcı öğeyi aradığında, arama değeri arama kutusunda tutulur.</span><span class="sxs-lookup"><span data-stu-id="f01ec-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="f01ec-172">Dizin görünümüne türe göre arama ekleme</span><span class="sxs-lookup"><span data-stu-id="f01ec-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="f01ec-173">`Index.cshtml` *Görünümler/Filmler/* aşağıdaki gibi bulunan güncelleştirme:</span><span class="sxs-lookup"><span data-stu-id="f01ec-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="f01ec-174">Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="f01ec-175">Önceki kodda, `DisplayNameFor` HTML Yardımcısı görüntü adını `Title` belirlemek için lambda ifadesinde başvurulan özelliği inceler.</span><span class="sxs-lookup"><span data-stu-id="f01ec-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="f01ec-176">Lambda `model`ifadesi değerlendirilmek yerine denetlendiğinden, "veya `model.Movies` `model.Movies[0]` `null` boş" olduğunda bir erişim ihlali almazsınız.</span><span class="sxs-lookup"><span data-stu-id="f01ec-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="f01ec-177">Lambda ifadesi değerlendirildiğinde (örneğin), `@Html.DisplayFor(modelItem => item.Title)`modelin özellik değerleri değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f01ec-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="f01ec-178">Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin:</span><span class="sxs-lookup"><span data-stu-id="f01ec-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Sonuçlarını gösteren tarayıcı penceresihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f01ec-180">[Önceki](controller-methods-views.md)
> [Sonraki](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="f01ec-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
