---
title: Bölüm 6, arama Ekle
author: rick-anderson
description: Sayfalardaki eğitim serisinin 6. bölümü Razor .
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: 1e571966b78f93e29e7901dd9648fbe3aca52726
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855488"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Bölüm 6, ASP.NET Core sayfalara arama ekleme Razor

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki vurgulanmış using ifadesini ve özelliklerini *sayfalara/filmlere/ Index . cshtml.cs* ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

Önceki kodda:

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString` özniteliği vardır [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) . `[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `[BindProperty(SupportsGet = true)]` HTTP GET isteklerinde bağlama için gereklidir.
* `Genres`: Tarzlar listesini içerir. `Genres` kullanıcının listeden bir tarz seçmesine izin verir. `SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Kullanıcının seçtiği belirli tarzı içerir. Örneğin, "Batı".
* `Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Yöntemin ilk satırı, `OnGetAsync` filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu yalnızca ***tanımlı** _, bu noktada veritabanına karşı çalıştırılmadı. _*_not_*_

`SearchString`Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır `Contains` . LINQ sorguları tanımlandıklarında veya, ya da gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` . Bunun yerine sorgu yürütmesi ertelenir. Bir ifadenin değerlendirilmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikiyor. Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` . Örneğin, `https://localhost:5001/Movies?searchString=Ghost`. Filtrelenmiş filmler görüntülenir.

![::: No-Loc (Dizin)::: görünüm](search/_static/ghost.png)

Aşağıdaki yol şablonu Index sayfaya eklenirse, arama dizesi BIR URL segmenti olarak geçirilebilir. Örneğin, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![::: No-Loc (Dizin)::: URL 'ye eklenen ve iki filmde döndürülen bir film listesi, Ghostbusters ve Ghostbusters ve 2](search/_static/g2.png)

ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` . Model bağlama büyük/küçük harfe duyarlı _*_değildir_*_ .

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesi beklenmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.

_Pages/movies/ Index . cshtml * dosyasını açın ve aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *sayfalara/filmlere/ Index* sayfaya gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![::: No-Loc (Dizin)::: başlık filtresi metin kutusuna hayalet sözcük türü görüntüle](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Sayfaya türe göre ara ekleme Razor

1. *Index . Cshtml* [ `<form>` element] öğesini ( https://developer.mozilla.org/docs/Web/HTML/Element/form) aşağıdaki biçimlendirmede vurgulanan şekilde) güncelleştirin:

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleyin](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/ Index . cshtml.cs* ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString` özniteliği vardır [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) . `[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `[BindProperty(SupportsGet = true)]` HTTP GET isteklerinde bağlama için gereklidir.
* `Genres`: Tarzlar listesini içerir. `Genres` kullanıcının listeden bir tarz seçmesine izin verir. `SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: Kullanıcının seçtiği belirli tarzı içerir. Örneğin, "Batı".
* `Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

IndexSayfanın `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Yöntemin ilk satırı, `OnGetAsync` filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**

`SearchString`Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır `Contains` . LINQ sorguları tanımlandıklarında veya gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` . Bunun yerine sorgu yürütmesi ertelenir. Bir ifadenin değerlendirilmesi, gerçekleştirilmiş değeri yinelenene veya `ToListAsync` Yöntem çağrılana kadar gecikiyor. Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

**Note:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` . Örneğin, `https://localhost:5001/Movies?searchString=Ghost`. Filtrelenmiş filmler görüntülenir.

![::: No-Loc (Dizin)::: görünüm](search/_static/ghost.png)

Aşağıdaki yol şablonu Index sayfaya eklenirse, arama dizesi BIR URL segmenti olarak geçirilebilir. Örneğin, `https://localhost:5001/Movies/Ghost`.

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![::: No-Loc (Dizin)::: URL 'ye eklenen ve iki filmde döndürülen bir film listesi, Ghostbusters ve Ghostbusters ve 2](search/_static/g2.png)

ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` . Model bağlama, _ büyük/küçük harfe duyarlı *_değildir_*.

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesi beklenmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.

_Pages/movies/ Index . cshtml * dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *sayfalara/filmlere/ Index* sayfaya gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![::: No-Loc (Dizin)::: başlık filtresi metin kutusuna hayalet sözcük türü görüntüle](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Sayfaya türe göre ara ekleme Razor

*Index . Cshtml* [ `<form>` element] öğesini ( https://developer.mozilla.org/docs/Web/HTML/Element/form) aşağıdaki biçimlendirmede vurgulanan şekilde) güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.
Önceki kod, [Select etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper) ve seçenek etiketi yardımcısını kullanır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleyin](xref:tutorials/razor-pages/new-field)

::: moniker-end
