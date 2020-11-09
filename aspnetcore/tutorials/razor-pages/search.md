---
title: Bölüm 6, arama Ekle
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 6. bölümü Razor .'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/search
ms.openlocfilehash: 00c1be2704d92c7d4f868e6eaa346bd8e9901dbf
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360848"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="f8011-103">Bölüm 6, ASP.NET Core sayfalara arama ekleme Razor</span><span class="sxs-lookup"><span data-stu-id="f8011-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="f8011-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8011-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f8011-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f8011-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="f8011-106">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f8011-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8011-107">Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="f8011-108">Aşağıdaki vurgulanmış using ifadesini ve özelliklerini *sayfalara/filmlere/ Index . cshtml.cs* ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f8011-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="f8011-109">Önceki kodda:</span><span class="sxs-lookup"><span data-stu-id="f8011-109">In the previous code:</span></span>

* <span data-ttu-id="f8011-110">`SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="f8011-111">`SearchString` özniteliği vardır [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f8011-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="f8011-112">`[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="f8011-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="f8011-113">`[BindProperty(SupportsGet = true)]` HTTP GET isteklerinde bağlama için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f8011-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="f8011-114">`Genres`: Tarzlar listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="f8011-115">`Genres` kullanıcının listeden bir tarz seçmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f8011-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="f8011-116">`SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="f8011-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="f8011-117">`MovieGenre`: Kullanıcının seçtiği belirli tarzı içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="f8011-118">Örneğin, "Batı".</span><span class="sxs-lookup"><span data-stu-id="f8011-118">For example, "Western".</span></span>
* <span data-ttu-id="f8011-119">`Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f8011-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f8011-120">IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="f8011-121">Yöntemin ilk satırı, `OnGetAsync` filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f8011-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f8011-122">Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**</span><span class="sxs-lookup"><span data-stu-id="f8011-122">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f8011-123">`SearchString`Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="f8011-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="f8011-124">`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f8011-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f8011-125">Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır `Contains` .</span><span class="sxs-lookup"><span data-stu-id="f8011-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="f8011-126">LINQ sorguları tanımlandıklarında veya, ya da gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="f8011-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="f8011-127">Bunun yerine sorgu yürütmesi ertelenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="f8011-128">Bir ifadenin değerlendirilmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikiyor.</span><span class="sxs-lookup"><span data-stu-id="f8011-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f8011-129">Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="f8011-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="f8011-130">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="f8011-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="f8011-131">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f8011-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f8011-132">SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f8011-133">SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f8011-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f8011-134">Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` .</span><span class="sxs-lookup"><span data-stu-id="f8011-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="f8011-135">Örneğin, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="f8011-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="f8011-136">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-136">The filtered movies are displayed.</span></span>

![::: No-Loc (Dizin)::: görünüm](search/_static/ghost.png)

<span data-ttu-id="f8011-138">Aşağıdaki yol şablonu Index sayfaya eklenirse, arama dizesi BIR URL segmenti olarak geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f8011-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="f8011-139">Örneğin, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="f8011-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="f8011-140">Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f8011-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="f8011-141">`?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f8011-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![::: No-Loc (Dizin)::: URL 'ye eklenen ve iki filmde döndürülen bir film listesi, Ghostbusters ve Ghostbusters ve 2](search/_static/g2.png)

<span data-ttu-id="f8011-143">ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` .</span><span class="sxs-lookup"><span data-stu-id="f8011-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="f8011-144">Model bağlama, _ büyük/küçük harfe duyarlı *_değildir_*.</span><span class="sxs-lookup"><span data-stu-id="f8011-144">Model binding is \* *_not_* _ case sensitive.</span></span>

<span data-ttu-id="f8011-145">Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesi beklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8011-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="f8011-146">Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="f8011-147">Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f8011-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="f8011-148">_Pages/movies/ Index . cshtml \* dosyasını açın ve aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f8011-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="f8011-149">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="f8011-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="f8011-150">[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f8011-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f8011-151">Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *sayfalara/filmlere/ Index* sayfaya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f8011-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="f8011-152">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="f8011-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="f8011-153">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="f8011-153">Save the changes and test the filter.</span></span>

![::: No-Loc (Dizin)::: başlık filtresi metin kutusuna hayalet sözcük türü görüntüle](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="f8011-155">Tarza göre ara</span><span class="sxs-lookup"><span data-stu-id="f8011-155">Search by genre</span></span>

<span data-ttu-id="f8011-156">IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f8011-157">Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="f8011-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="f8011-158">`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f8011-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="f8011-159">Sayfaya türe göre ara ekleme Razor</span><span class="sxs-lookup"><span data-stu-id="f8011-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="f8011-160">*Index . Cshtml* [ `<form>` element] öğesini ( https://developer.mozilla.org/docs/Web/HTML/Element/form) aşağıdaki biçimlendirmede vurgulanan şekilde) güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="f8011-161">Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="f8011-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8011-162">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f8011-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f8011-163">[Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleyin](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="f8011-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8011-164">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f8011-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f8011-165">Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="f8011-166">Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/ Index . cshtml.cs* ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f8011-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs* :</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="f8011-167">`SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="f8011-168">`SearchString` özniteliği vardır [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) .</span><span class="sxs-lookup"><span data-stu-id="f8011-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="f8011-169">`[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar.</span><span class="sxs-lookup"><span data-stu-id="f8011-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="f8011-170">`[BindProperty(SupportsGet = true)]` HTTP GET isteklerinde bağlama için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f8011-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="f8011-171">`Genres`: Tarzlar listesini içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="f8011-172">`Genres` kullanıcının listeden bir tarz seçmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="f8011-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="f8011-173">`SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="f8011-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="f8011-174">`MovieGenre`: Kullanıcının seçtiği belirli tarzı içerir.</span><span class="sxs-lookup"><span data-stu-id="f8011-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="f8011-175">Örneğin, "Batı".</span><span class="sxs-lookup"><span data-stu-id="f8011-175">For example, "Western".</span></span>
* <span data-ttu-id="f8011-176">`Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f8011-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f8011-177">IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="f8011-178">Yöntemin ilk satırı, `OnGetAsync` filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f8011-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f8011-179">Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**</span><span class="sxs-lookup"><span data-stu-id="f8011-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f8011-180">`SearchString`Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:</span><span class="sxs-lookup"><span data-stu-id="f8011-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="f8011-181">`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f8011-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f8011-182">Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır `Contains` .</span><span class="sxs-lookup"><span data-stu-id="f8011-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="f8011-183">LINQ sorguları tanımlandıklarında veya gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` .</span><span class="sxs-lookup"><span data-stu-id="f8011-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="f8011-184">Bunun yerine sorgu yürütmesi ertelenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="f8011-185">Bir ifadenin değerlendirilmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikiyor.</span><span class="sxs-lookup"><span data-stu-id="f8011-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f8011-186">Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="f8011-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="f8011-187">**Note:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="f8011-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="f8011-188">Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f8011-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f8011-189">SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f8011-190">SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="f8011-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f8011-191">Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` .</span><span class="sxs-lookup"><span data-stu-id="f8011-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="f8011-192">Örneğin, `https://localhost:5001/Movies?searchString=Ghost`.</span><span class="sxs-lookup"><span data-stu-id="f8011-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="f8011-193">Filtrelenmiş filmler görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-193">The filtered movies are displayed.</span></span>

![::: No-Loc (Dizin)::: görünüm](search/_static/ghost.png)

<span data-ttu-id="f8011-195">Aşağıdaki yol şablonu Index sayfaya eklenirse, arama dizesi BIR URL segmenti olarak geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f8011-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="f8011-196">Örneğin, `https://localhost:5001/Movies/Ghost`.</span><span class="sxs-lookup"><span data-stu-id="f8011-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="f8011-197">Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f8011-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="f8011-198">`?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f8011-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![::: No-Loc (Dizin)::: URL 'ye eklenen ve iki filmde döndürülen bir film listesi, Ghostbusters ve Ghostbusters ve 2](search/_static/g2.png)

<span data-ttu-id="f8011-200">ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` .</span><span class="sxs-lookup"><span data-stu-id="f8011-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="f8011-201">Model bağlama, _ büyük/küçük harfe duyarlı *_değildir_*.</span><span class="sxs-lookup"><span data-stu-id="f8011-201">Model binding is \* *_not_* _ case sensitive.</span></span>

<span data-ttu-id="f8011-202">Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesi beklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8011-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="f8011-203">Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir.</span><span class="sxs-lookup"><span data-stu-id="f8011-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="f8011-204">Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f8011-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="f8011-205">_Pages/movies/ Index . cshtml \* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f8011-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="f8011-206">HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:</span><span class="sxs-lookup"><span data-stu-id="f8011-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="f8011-207">[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="f8011-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="f8011-208">Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *sayfalara/filmlere/ Index* sayfaya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f8011-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="f8011-209">Giriş Etiketi Yardımcısı</span><span class="sxs-lookup"><span data-stu-id="f8011-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="f8011-210">Değişiklikleri kaydedin ve filtreyi test edin.</span><span class="sxs-lookup"><span data-stu-id="f8011-210">Save the changes and test the filter.</span></span>

![::: No-Loc (Dizin)::: başlık filtresi metin kutusuna hayalet sözcük türü görüntüle](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="f8011-212">Tarza göre ara</span><span class="sxs-lookup"><span data-stu-id="f8011-212">Search by genre</span></span>

<span data-ttu-id="f8011-213">`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="f8011-214">Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.</span><span class="sxs-lookup"><span data-stu-id="f8011-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="f8011-215">`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="f8011-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="f8011-216">Sayfaya türe göre ara ekleme Razor</span><span class="sxs-lookup"><span data-stu-id="f8011-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="f8011-217">*Index . Cshtml* [ `<form>` element] öğesini ( https://developer.mozilla.org/docs/Web/HTML/Element/form) aşağıdaki biçimlendirmede vurgulanan şekilde) güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f8011-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="f8011-218">Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.</span><span class="sxs-lookup"><span data-stu-id="f8011-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="f8011-219">Önceki kod, [Select etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper) ve seçenek etiketi yardımcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="f8011-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8011-220">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f8011-220">Additional resources</span></span>

* [<span data-ttu-id="f8011-221">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="f8011-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="f8011-222">[Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleyin](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="f8011-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
