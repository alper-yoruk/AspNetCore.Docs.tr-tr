---
title: Bölüm 6, ASP.NET Core sayfalara arama ekleme Razor
author: rick-anderson
description: Sayfalardaki eğitim serisinin 6. bölümü Razor .
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 960f60198f5e65ed05d0374fd0704537376d27d6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058096"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a>Bölüm 6, ASP.NET Core sayfalara arama ekleme Razor

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs* öğesine ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString` özniteliği vardır [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) . `[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `(SupportsGet = true)` GET isteklerinde bağlama için gereklidir.
* `Genres`: tarzlar listesini içerir. `Genres` kullanıcının listeden bir tarz seçmesine izin verir. `SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").
* `Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

Yöntemin ilk satırı, `OnGetAsync` filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**

`SearchString`Özellik null veya boş değilse, filmler sorgusu arama dizesinde filtrelenecek şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır. LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where` , veya) çağırarak değiştirildiklerinde yürütülmez `Contains` `OrderBy` . Bunun yerine sorgu yürütmesi ertelenir. Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya yöntem çağrılana kadar gecikir `ToListAsync` . Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

> [!NOTE]
> [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` (örneğin, `https://localhost:5001/Movies?searchString=Ghost` ). Filtrelenmiş filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` . Model bağlama büyük/küçük harfe duyarlı değildir.

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.

*Pages/filmler/Index. cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Sayfaya türe göre ara ekleme Razor

*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, film *tarzya* veya *ada* göre arama eklenir.

Aşağıdaki Vurgulanan özellikleri *sayfalara/filmlere/Index. cshtml. cs* öğesine ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: Kullanıcılar arama metin kutusuna girdiğiniz metni içerir. `SearchString` özniteliği vardır [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) . `[BindProperty]` Form değerlerini ve Sorgu dizelerini özelliği ile aynı ada bağlar. `(SupportsGet = true)` GET isteklerinde bağlama için gereklidir.
* `Genres`: tarzlar listesini içerir. `Genres` kullanıcının listeden bir tarz seçmesine izin verir. `SelectList` gerektirmeyen `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği belirli tarzı içerir (örneğin, "Batı").
* `Genres` ve `MovieGenre` Bu öğreticide daha sonra kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

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

`s => s.Title.Contains()`Kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında, [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) yöntemi veya `Contains` (önceki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır. LINQ sorguları tanımlandıklarında veya bir Yöntem (örneğin `Where` , veya) çağırarak değiştirildiklerinde yürütülmez `Contains` `OrderBy` . Bunun yerine sorgu yürütmesi ertelenir. Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri yinelenene veya yöntem çağrılana kadar gecikir `ToListAsync` . Daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

**Note:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi C# kodunda değil veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server, `Contains` büyük/küçük harfe duyarsız olan [SQL](/sql/t-sql/language-elements/like-transact-sql)ile eşlenir. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

Filmler sayfasına gidin ve URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` (örneğin, `https://localhost:5001/Movies?searchString=Ghost` ). Filtrelenmiş filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Aşağıdaki yol şablonu dizin sayfasına eklendiyse, arama dizesi bir URL segmenti olarak geçirilebilir (örneğin, `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Önceki yol kısıtlaması, başlığın sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak aranmasına olanak tanır.  `?`İçinde `"{searchString?}"` bunun isteğe bağlı bir yol parametresi olduğu anlamına gelir.

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](search/_static/g2.png)

ASP.NET Core çalışma zamanı, [model binding](xref:mvc/models/model-binding) `SearchString` özelliğin değerini sorgu dizesinden ( `?searchString=Ghost` ) veya rota verilerinden () ayarlamak için model bağlamayı kullanır `https://localhost:5001/Movies/Ghost` . Model bağlama büyük/küçük harfe duyarlı değildir.

Ancak, kullanıcıların bir filmi aramak için URL 'YI değiştirmesini beklemeniz gerekmez. Bu adımda, filmleri filtrelemek için Kullanıcı arabirimi eklenir. Yol kısıtlaması eklediyseniz `"{searchString?}"` , kaldırın.

*Pages/filmler/Index. cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi, sorgu dizesi aracılığıyla *Sayfalar/filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Tarza göre ara

`OnGetAsync`Yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm tarzları alan bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Tarzlar, farklı tarzlar yansıtılayarak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a>Sayfaya türe göre ara ekleme Razor

*Index. cshtml* 'yi aşağıdaki şekilde güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin.
Önceki kod, [Select etiketi yardımcısını](xref:mvc/views/working-with-forms#the-select-tag-helper) ve seçenek etiketi yardımcısını kullanır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1) 
>  [Sonraki: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end
