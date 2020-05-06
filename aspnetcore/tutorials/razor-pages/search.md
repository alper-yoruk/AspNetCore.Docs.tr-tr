---
title: ASP.NET Core Razor sayfalarına arama ekleme
author: rick-anderson
description: ASP.NET Core Razor sayfalarına aramanın nasıl ekleneceğini gösterir
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: baa5e1cb2098a60155a4196f0e602feeff04f102
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775004"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="30814-103">ASP.NET Core Razor sayfalarına arama ekleme</span><span class="sxs-lookup"><span data-stu-id="30814-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="30814-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="30814-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="30814-105">Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.</span><span class="sxs-lookup"><span data-stu-id="30814-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="30814-106">Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="30814-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="30814-107">`SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir.</span><span class="sxs-lookup"><span data-stu-id="30814-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="30814-108">`SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="30814-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="30814-109">`[BindProperty]`Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="30814-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="30814-110">`(SupportsGet = true)`GET isteklerinde bağlama için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="30814-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="30814-111">`Genres`: tarzlar listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="30814-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="30814-112">`Genres`kullanıcının listeden bir tarz seçmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="30814-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="30814-113">`SelectList`gerektirmeyen`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="30814-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="30814-114">`MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").</span><span class="sxs-lookup"><span data-stu-id="30814-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="30814-115">`Genres`ve `MovieGenre` Bu öğreticide daha sonra kullanılır.</span><span class="sxs-lookup"><span data-stu-id="30814-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="30814-116">Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="30814-117">`OnGetAsync` Yöntemin ilk satırı, filmleri seçmek Için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="30814-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="30814-118">Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**</span><span class="sxs-lookup"><span data-stu-id="30814-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="30814-119">`SearchString` Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="30814-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="30814-120">`s => s.Title.Contains()` Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="30814-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="30814-121">Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="30814-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="30814-122">LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where`, `Contains` veya `OrderBy`) çağırarak değiştirildiklerinde yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="30814-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="30814-123">Bunun yerine sorgu yürütmesi ertelenir.</span><span class="sxs-lookup"><span data-stu-id="30814-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="30814-124">Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikir.</span><span class="sxs-lookup"><span data-stu-id="30814-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="30814-125">Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="30814-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="30814-126">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="30814-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="30814-127">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="30814-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="30814-128">SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir.</span><span class="sxs-lookup"><span data-stu-id="30814-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="30814-129">SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="30814-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="30814-130">Filmler sayfasına gidin ve URL `?searchString=Ghost` 'ye gibi bir sorgu dizesi ekleyin (örneğin, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30814-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="30814-131">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="30814-131">The filtered movies are displayed.</span></span>

![Dizin görünümü](search/_static/ghost.png)

<span data-ttu-id="30814-133">Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30814-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="30814-134">Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="30814-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="30814-135">`?` İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="30814-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

<span data-ttu-id="30814-137">ASP.NET Core çalışma zamanı, `SearchString` özelliğin değerini sorgu dizesinden (`?searchString=Ghost`) veya rota verilerinden (`https://localhost:5001/Movies/Ghost`) ayarlamak için [model bağlamayı](xref:mvc/models/model-binding) kullanır.</span><span class="sxs-lookup"><span data-stu-id="30814-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="30814-138">Model bağlama büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="30814-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="30814-139">Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="30814-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="30814-140">Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="30814-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="30814-141">Yol kısıtlaması `"{searchString?}"`eklediyseniz, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="30814-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="30814-142">*Pages/filmler/Index. cshtml* dosyasını açın ve aşağıdaki kodda vurgulanan `<form>` biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="30814-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="30814-143">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="30814-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="30814-144">[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="30814-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="30814-145">Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="30814-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="30814-146">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="30814-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="30814-147">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="30814-147">Save the changes and test the filter.</span></span>

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="30814-149">Tarza göre ara</span><span class="sxs-lookup"><span data-stu-id="30814-149">Search by genre</span></span>

<span data-ttu-id="30814-150">`OnGetAsync` Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="30814-151">Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="30814-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="30814-152">Tarzlar, `SelectList` farklı tarzlar yansıtılayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="30814-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="30814-153">Razor Sayfaya türe göre ara ekleme</span><span class="sxs-lookup"><span data-stu-id="30814-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="30814-154">*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="30814-155">Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="30814-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30814-156">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="30814-156">Additional resources</span></span>

* [<span data-ttu-id="30814-157">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="30814-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="30814-158">[Önceki: sonraki sayfaları](xref:tutorials/razor-pages/da1)
> güncelleştirme[: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="30814-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="30814-159">Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.</span><span class="sxs-lookup"><span data-stu-id="30814-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="30814-160">Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs*öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="30814-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="30814-161">`SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir.</span><span class="sxs-lookup"><span data-stu-id="30814-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="30814-162">`SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır.</span><span class="sxs-lookup"><span data-stu-id="30814-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="30814-163">`[BindProperty]`Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="30814-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="30814-164">`(SupportsGet = true)`GET isteklerinde bağlama için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="30814-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="30814-165">`Genres`: tarzlar listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="30814-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="30814-166">`Genres`kullanıcının listeden bir tarz seçmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="30814-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="30814-167">`SelectList`gerektirmeyen`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="30814-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="30814-168">`MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").</span><span class="sxs-lookup"><span data-stu-id="30814-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="30814-169">`Genres`ve `MovieGenre` Bu öğreticide daha sonra kullanılır.</span><span class="sxs-lookup"><span data-stu-id="30814-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="30814-170">Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="30814-171">`OnGetAsync` Yöntemin ilk satırı, filmleri seçmek Için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="30814-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="30814-172">Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**</span><span class="sxs-lookup"><span data-stu-id="30814-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="30814-173">`SearchString` Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="30814-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="30814-174">`s => s.Title.Contains()` Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="30814-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="30814-175">Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="30814-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="30814-176">LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where`, `Contains` veya `OrderBy`) çağırarak değiştirildiklerinde yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="30814-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="30814-177">Bunun yerine sorgu yürütmesi ertelenir.</span><span class="sxs-lookup"><span data-stu-id="30814-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="30814-178">Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikir.</span><span class="sxs-lookup"><span data-stu-id="30814-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="30814-179">Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="30814-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="30814-180">**Note:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="30814-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="30814-181">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="30814-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="30814-182">SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir.</span><span class="sxs-lookup"><span data-stu-id="30814-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="30814-183">SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="30814-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="30814-184">Filmler sayfasına gidin ve URL `?searchString=Ghost` 'ye gibi bir sorgu dizesi ekleyin (örneğin, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30814-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="30814-185">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="30814-185">The filtered movies are displayed.</span></span>

![Dizin görünümü](search/_static/ghost.png)

<span data-ttu-id="30814-187">Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="30814-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="30814-188">Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="30814-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="30814-189">`?` İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="30814-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

<span data-ttu-id="30814-191">ASP.NET Core çalışma zamanı, `SearchString` özelliğin değerini sorgu dizesinden (`?searchString=Ghost`) veya rota verilerinden (`https://localhost:5001/Movies/Ghost`) ayarlamak için [model bağlamayı](xref:mvc/models/model-binding) kullanır.</span><span class="sxs-lookup"><span data-stu-id="30814-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="30814-192">Model bağlama büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="30814-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="30814-193">Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="30814-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="30814-194">Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="30814-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="30814-195">Yol kısıtlaması `"{searchString?}"`eklediyseniz, kaldırın.</span><span class="sxs-lookup"><span data-stu-id="30814-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="30814-196">*Pages/filmler/Index. cshtml* dosyasını açın ve aşağıdaki kodda vurgulanan `<form>` biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="30814-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="30814-197">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="30814-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="30814-198">[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="30814-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="30814-199">Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.</span><span class="sxs-lookup"><span data-stu-id="30814-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="30814-200">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="30814-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="30814-201">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="30814-201">Save the changes and test the filter.</span></span>

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="30814-203">Tarza göre ara</span><span class="sxs-lookup"><span data-stu-id="30814-203">Search by genre</span></span>

<span data-ttu-id="30814-204">`OnGetAsync` Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="30814-205">Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="30814-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="30814-206">Tarzlar, `SelectList` farklı tarzlar yansıtılayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="30814-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="30814-207">Razor Sayfaya türe göre ara ekleme</span><span class="sxs-lookup"><span data-stu-id="30814-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="30814-208">*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="30814-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="30814-209">Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="30814-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="30814-210">Önceki kod, [Select etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper) ve seçenek etiketi yardımcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="30814-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30814-211">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="30814-211">Additional resources</span></span>

* [<span data-ttu-id="30814-212">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="30814-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="30814-213">[Önceki: sonraki sayfaları](xref:tutorials/razor-pages/da1)
> güncelleştirme[: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="30814-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
