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
# <a name="add-search-to-aspnet-core-razor-pages"></a>ASP.NET Core Razor sayfalarına arama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs*öğesine ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır. `[BindProperty]`Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `(SupportsGet = true)`GET isteklerinde bağlama için gereklidir.
* `Genres`: tarzlar listesini içerir. `Genres`kullanıcının listeden bir tarz seçmesine izin verir. `SelectList`gerektirmeyen`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").
* `Genres`ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` Yöntemin ilk satırı, filmleri seçmek Için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**

`SearchString` Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır. LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where`, `Contains` veya `OrderBy`) çağırarak değiştirildiklerinde yürütülmez. Bunun yerine sorgu yürütmesi ertelenir. Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikir. Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL `?searchString=Ghost` 'ye gibi bir sorgu dizesi ekleyin (örneğin, `https://localhost:5001/Movies?searchString=Ghost`). Filtrelenmiş filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?` İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

ASP.NET Core çalışma zamanı, `SearchString` özelliğin değerini sorgu dizesinden (`?searchString=Ghost`) veya rota verilerinden (`https://localhost:5001/Movies/Ghost`) ayarlamak için [model bağlamayı](xref:mvc/models/model-binding) kullanır. Model bağlama büyük/küçük harfe duyarlı değildir.

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması `"{searchString?}"`eklediyseniz, kaldırın.

*Pages/filmler/Index. cshtml* dosyasını açın ve aşağıdaki kodda vurgulanan `<form>` biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

`OnGetAsync` Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Tarzlar, `SelectList` farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Razor Sayfaya türe göre ara ekleme

*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: sonraki sayfaları](xref:tutorials/razor-pages/da1)
> güncelleştirme[: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs*öğesine ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır. `[BindProperty]`Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `(SupportsGet = true)`GET isteklerinde bağlama için gereklidir.
* `Genres`: tarzlar listesini içerir. `Genres`kullanıcının listeden bir tarz seçmesine izin verir. `SelectList`gerektirmeyen`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").
* `Genres`ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` Yöntemin ilk satırı, filmleri seçmek Için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**

`SearchString` Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır. LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where`, `Contains` veya `OrderBy`) çağırarak değiştirildiklerinde yürütülmez. Bunun yerine sorgu yürütmesi ertelenir. Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikir. Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

**Note:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL `?searchString=Ghost` 'ye gibi bir sorgu dizesi ekleyin (örneğin, `https://localhost:5001/Movies?searchString=Ghost`). Filtrelenmiş filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?` İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

ASP.NET Core çalışma zamanı, `SearchString` özelliğin değerini sorgu dizesinden (`?searchString=Ghost`) veya rota verilerinden (`https://localhost:5001/Movies/Ghost`) ayarlamak için [model bağlamayı](xref:mvc/models/model-binding) kullanır. Model bağlama büyük/küçük harfe duyarlı değildir.

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması `"{searchString?}"`eklediyseniz, kaldırın.

*Pages/filmler/Index. cshtml* dosyasını açın ve aşağıdaki kodda vurgulanan `<form>` biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

`OnGetAsync` Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Tarzlar, `SelectList` farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Razor Sayfaya türe göre ara ekleme

*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.
Önceki kod, [Select etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper) ve seçenek etiketi yardımcısını kullanır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: sonraki sayfaları](xref:tutorials/razor-pages/da1)
> güncelleştirme[: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end
