---
title: ASP.NET Çekirdek Jilet Sayfalarına arama ekleme
author: rick-anderson
description: ASP.NET Core Razor Sayfalarına arama eklemenin nasıl yapılacağını gösterir
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667708"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="02957-103">ASP.NET Çekirdek Jilet Sayfalarına arama ekleme</span><span class="sxs-lookup"><span data-stu-id="02957-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="02957-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02957-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="02957-105">Aşağıdaki bölümlerde, *tür* e veya *ada* göre film arama sı eklenir.</span><span class="sxs-lookup"><span data-stu-id="02957-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="02957-106">*Sayfalar/Filmler/Index.cshtml.cs*aşağıdaki vurgulanan özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02957-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="02957-107">`SearchString`: kullanıcıların arama metin kutusuna girdikleri metni içerir.</span><span class="sxs-lookup"><span data-stu-id="02957-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="02957-108">`SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="02957-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="02957-109">`[BindProperty]`değerleri ve sorgu dizelerini özellik ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="02957-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="02957-110">`(SupportsGet = true)`GET isteklerini bağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02957-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="02957-111">`Genres`: türlerin listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="02957-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="02957-112">`Genres`kullanıcının listeden bir tür seçmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="02957-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="02957-113">`SelectList`Gerektirir`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="02957-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="02957-114">`MovieGenre`: kullanıcının seçtiği özel türü içerir (örneğin, "Western").</span><span class="sxs-lookup"><span data-stu-id="02957-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="02957-115">`Genres`ve `MovieGenre` daha sonra bu öğretici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02957-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="02957-116">Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="02957-117">`OnGetAsync` Yöntemin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="02957-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="02957-118">Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**</span><span class="sxs-lookup"><span data-stu-id="02957-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="02957-119">`SearchString` Özellik boş veya boş değilse, film sorgusu arama dizesine filtre uygulayacak şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="02957-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="02957-120">Kod `s => s.Title.Contains()` bir [Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)İfadesi.</span><span class="sxs-lookup"><span data-stu-id="02957-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="02957-121">Lambdas, yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metodu veya `Contains` (önceki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02957-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="02957-122">LINQ sorguları tanımlandıkları nda veya bir metodu `Where`(örneğin, `Contains` veya) `OrderBy`çağırArak değiştirildiğinde yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="02957-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="02957-123">Bunun yerine, sorgu yürütme ertelendi.</span><span class="sxs-lookup"><span data-stu-id="02957-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="02957-124">Bu, bir ifadenin değerlendirilmesinin gerçekleşen değeri üzerinde tekrarlanana veya yöntem çağrılana `ToListAsync` kadar geciktiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="02957-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="02957-125">Daha fazla bilgi için [Sorgu Yürütme'ye](/dotnet/framework/data/adonet/ef/language-reference/query-execution) bakın.</span><span class="sxs-lookup"><span data-stu-id="02957-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="02957-126">[İçle metodu](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) C# kodunda değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="02957-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="02957-127">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="02957-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="02957-128">SQL Server'da, `Contains` sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)ile eşler, bu durum duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="02957-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="02957-129">SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.</span><span class="sxs-lookup"><span data-stu-id="02957-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="02957-130">Filmler sayfasına gidin ve URL gibi `?searchString=Ghost` bir sorgu dizesini `https://localhost:5001/Movies?searchString=Ghost`ekleyin (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="02957-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="02957-131">Filtre uygulanmış filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="02957-131">The filtered movies are displayed.</span></span>

![Dizin görünümü](search/_static/ghost.png)

<span data-ttu-id="02957-133">Dizin sayfasına aşağıdaki rota şablonu eklenirse, arama dizesi BIR `https://localhost:5001/Movies/Ghost`URL kesimi olarak geçirilebilir (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="02957-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="02957-134">Önceki rota kısıtlaması, başlığı sorgu dize değeri yerine rota verisi (URL kesimi) olarak aramayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="02957-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="02957-135">In `?` `"{searchString?}"` bu isteğe bağlı bir rota parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="02957-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](search/_static/g2.png)

<span data-ttu-id="02957-137">ASP.NET Core çalışma süresi, `SearchString` sorgu dizesi ( ) veya`?searchString=Ghost`rota verilerinden`https://localhost:5001/Movies/Ghost`( ) özelliğin değerini ayarlamak için [model bağlama](xref:mvc/models/model-binding) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02957-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="02957-138">Model bağlama büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="02957-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="02957-139">Ancak, kullanıcıların bir film aramak için URL'yi değiştirmesini bekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="02957-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="02957-140">Bu adımda, Film filtrelemek için UI eklenir.</span><span class="sxs-lookup"><span data-stu-id="02957-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="02957-141">Rota kısıtlamasını `"{searchString?}"`eklediyseniz, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="02957-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="02957-142">*Sayfalar/Filmler/Index.cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02957-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="02957-143">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="02957-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="02957-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02957-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="02957-145">Form gönderildiğinde, filtre dizesi sorgu dizesi aracılığıyla *Sayfalar/Filmler/Dizin* sayfasına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="02957-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="02957-146">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="02957-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="02957-147">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="02957-147">Save the changes and test the filter.</span></span>

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="02957-149">Türe göre ara</span><span class="sxs-lookup"><span data-stu-id="02957-149">Search by genre</span></span>

<span data-ttu-id="02957-150">Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="02957-151">Aşağıdaki kod, veritabanından tüm türler alır bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="02957-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="02957-152">`SelectList` Türlerin farklı türler yansıtArak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02957-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="02957-153">Razor Sayfasına türe göre arama ekleme</span><span class="sxs-lookup"><span data-stu-id="02957-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="02957-154">*Index.cshtml'i* aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="02957-155">Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin.</span><span class="sxs-lookup"><span data-stu-id="02957-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02957-156">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="02957-156">Additional resources</span></span>

* [<span data-ttu-id="02957-157">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="02957-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="02957-158">[Önceki: Sonraki sayfaları](xref:tutorials/razor-pages/da1)
> [güncelleştirme: Yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="02957-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="02957-159">Aşağıdaki bölümlerde, *tür* e veya *ada* göre film arama sı eklenir.</span><span class="sxs-lookup"><span data-stu-id="02957-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="02957-160">*Sayfalar/Filmler/Index.cshtml.cs*aşağıdaki vurgulanan özellikleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02957-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="02957-161">`SearchString`: kullanıcıların arama metin kutusuna girdikleri metni içerir.</span><span class="sxs-lookup"><span data-stu-id="02957-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="02957-162">`SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="02957-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="02957-163">`[BindProperty]`değerleri ve sorgu dizelerini özellik ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="02957-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="02957-164">`(SupportsGet = true)`GET isteklerini bağlamak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="02957-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="02957-165">`Genres`: türlerin listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="02957-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="02957-166">`Genres`kullanıcının listeden bir tür seçmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="02957-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="02957-167">`SelectList`Gerektirir`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="02957-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="02957-168">`MovieGenre`: kullanıcının seçtiği özel türü içerir (örneğin, "Western").</span><span class="sxs-lookup"><span data-stu-id="02957-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="02957-169">`Genres`ve `MovieGenre` daha sonra bu öğretici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02957-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="02957-170">Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="02957-171">`OnGetAsync` Yöntemin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="02957-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="02957-172">Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**</span><span class="sxs-lookup"><span data-stu-id="02957-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="02957-173">`SearchString` Özellik boş veya boş değilse, film sorgusu arama dizesine filtre uygulayacak şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="02957-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="02957-174">Kod `s => s.Title.Contains()` bir [Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)İfadesi.</span><span class="sxs-lookup"><span data-stu-id="02957-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="02957-175">Lambdas, yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metodu veya `Contains` (önceki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="02957-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="02957-176">LINQ sorguları tanımlandıkları nda veya bir metodu `Where`(örneğin, `Contains` veya) `OrderBy`çağırArak değiştirildiğinde yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="02957-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="02957-177">Bunun yerine, sorgu yürütme ertelendi.</span><span class="sxs-lookup"><span data-stu-id="02957-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="02957-178">Bu, bir ifadenin değerlendirilmesinin gerçekleşen değeri üzerinde tekrarlanana veya yöntem çağrılana `ToListAsync` kadar geciktiği anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="02957-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="02957-179">Daha fazla bilgi için [Sorgu Yürütme'ye](/dotnet/framework/data/adonet/ef/language-reference/query-execution) bakın.</span><span class="sxs-lookup"><span data-stu-id="02957-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="02957-180">**Not:** [İçle metodu](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) C# kodunda değil, veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="02957-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="02957-181">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="02957-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="02957-182">SQL Server'da, `Contains` sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)ile eşler, bu durum duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="02957-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="02957-183">SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.</span><span class="sxs-lookup"><span data-stu-id="02957-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="02957-184">Filmler sayfasına gidin ve URL gibi `?searchString=Ghost` bir sorgu dizesini `https://localhost:5001/Movies?searchString=Ghost`ekleyin (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="02957-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="02957-185">Filtre uygulanmış filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="02957-185">The filtered movies are displayed.</span></span>

![Dizin görünümü](search/_static/ghost.png)

<span data-ttu-id="02957-187">Dizin sayfasına aşağıdaki rota şablonu eklenirse, arama dizesi BIR `https://localhost:5001/Movies/Ghost`URL kesimi olarak geçirilebilir (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="02957-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="02957-188">Önceki rota kısıtlaması, başlığı sorgu dize değeri yerine rota verisi (URL kesimi) olarak aramayı sağlar.</span><span class="sxs-lookup"><span data-stu-id="02957-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="02957-189">In `?` `"{searchString?}"` bu isteğe bağlı bir rota parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="02957-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](search/_static/g2.png)

<span data-ttu-id="02957-191">ASP.NET Core çalışma süresi, `SearchString` sorgu dizesi ( ) veya`?searchString=Ghost`rota verilerinden`https://localhost:5001/Movies/Ghost`( ) özelliğin değerini ayarlamak için [model bağlama](xref:mvc/models/model-binding) kullanır.</span><span class="sxs-lookup"><span data-stu-id="02957-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="02957-192">Model bağlama büyük/küçük harf duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="02957-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="02957-193">Ancak, kullanıcıların bir film aramak için URL'yi değiştirmesini bekleyemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="02957-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="02957-194">Bu adımda, Film filtrelemek için UI eklenir.</span><span class="sxs-lookup"><span data-stu-id="02957-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="02957-195">Rota kısıtlamasını `"{searchString?}"`eklediyseniz, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="02957-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="02957-196">*Sayfalar/Filmler/Index.cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02957-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="02957-197">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="02957-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="02957-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02957-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="02957-199">Form gönderildiğinde, filtre dizesi sorgu dizesi aracılığıyla *Sayfalar/Filmler/Dizin* sayfasına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="02957-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="02957-200">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="02957-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="02957-201">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="02957-201">Save the changes and test the filter.</span></span>

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="02957-203">Türe göre ara</span><span class="sxs-lookup"><span data-stu-id="02957-203">Search by genre</span></span>

<span data-ttu-id="02957-204">Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="02957-205">Aşağıdaki kod, veritabanından tüm türler alır bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="02957-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="02957-206">`SelectList` Türlerin farklı türler yansıtArak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="02957-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="02957-207">Razor Sayfasına türe göre arama ekleme</span><span class="sxs-lookup"><span data-stu-id="02957-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="02957-208">*Index.cshtml'i* aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="02957-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="02957-209">Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin.</span><span class="sxs-lookup"><span data-stu-id="02957-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="02957-210">Önceki kod, Tut Yardımcısı nı ve Seçenek Etiketi Yardımcısını [seçin'](xref:mvc/views/working-with-forms#the-select-tag-helper) i kullanır.</span><span class="sxs-lookup"><span data-stu-id="02957-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02957-211">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="02957-211">Additional resources</span></span>

* [<span data-ttu-id="02957-212">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="02957-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="02957-213">[Önceki: Sonraki sayfaları](xref:tutorials/razor-pages/da1)
> [güncelleştirme: Yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="02957-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
