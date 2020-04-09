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
# <a name="add-search-to-aspnet-core-razor-pages"></a>ASP.NET Çekirdek Jilet Sayfalarına arama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, *tür* e veya *ada* göre film arama sı eklenir.

*Sayfalar/Filmler/Index.cshtml.cs*aşağıdaki vurgulanan özellikleri ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: kullanıcıların arama metin kutusuna girdikleri metni içerir. `SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır. `[BindProperty]`değerleri ve sorgu dizelerini özellik ile aynı ada bağlar. `(SupportsGet = true)`GET isteklerini bağlamak için gereklidir.
* `Genres`: türlerin listesini içerir. `Genres`kullanıcının listeden bir tür seçmesine olanak tanır. `SelectList`Gerektirir`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği özel türü içerir (örneğin, "Western").
* `Genres`ve `MovieGenre` daha sonra bu öğretici kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` Yöntemin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**

`SearchString` Özellik boş veya boş değilse, film sorgusu arama dizesine filtre uygulayacak şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kod `s => s.Title.Contains()` bir [Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)İfadesi. Lambdas, yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metodu veya `Contains` (önceki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır. LINQ sorguları tanımlandıkları nda veya bir metodu `Where`(örneğin, `Contains` veya) `OrderBy`çağırArak değiştirildiğinde yürütülmez. Bunun yerine, sorgu yürütme ertelendi. Bu, bir ifadenin değerlendirilmesinin gerçekleşen değeri üzerinde tekrarlanana veya yöntem çağrılana `ToListAsync` kadar geciktiği anlamına gelir. Daha fazla bilgi için [Sorgu Yürütme'ye](/dotnet/framework/data/adonet/ef/language-reference/query-execution) bakın.

> [!NOTE]
> [İçle metodu](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) C# kodunda değil, veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır. SQL Server'da, `Contains` sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)ile eşler, bu durum duyarsızdır. SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.

Filmler sayfasına gidin ve URL gibi `?searchString=Ghost` bir sorgu dizesini `https://localhost:5001/Movies?searchString=Ghost`ekleyin (örneğin, ). Filtre uygulanmış filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Dizin sayfasına aşağıdaki rota şablonu eklenirse, arama dizesi BIR `https://localhost:5001/Movies/Ghost`URL kesimi olarak geçirilebilir (örneğin, ).

```cshtml
@page "{searchString?}"
```

Önceki rota kısıtlaması, başlığı sorgu dize değeri yerine rota verisi (URL kesimi) olarak aramayı sağlar.  In `?` `"{searchString?}"` bu isteğe bağlı bir rota parametresi olduğu anlamına gelir.

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](search/_static/g2.png)

ASP.NET Core çalışma süresi, `SearchString` sorgu dizesi ( ) veya`?searchString=Ghost`rota verilerinden`https://localhost:5001/Movies/Ghost`( ) özelliğin değerini ayarlamak için [model bağlama](xref:mvc/models/model-binding) kullanır. Model bağlama büyük/küçük harf duyarlı değildir.

Ancak, kullanıcıların bir film aramak için URL'yi değiştirmesini bekleyemezsiniz. Bu adımda, Film filtrelemek için UI eklenir. Rota kısıtlamasını `"{searchString?}"`eklediyseniz, kaldırın.

*Sayfalar/Filmler/Index.cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi sorgu dizesi aracılığıyla *Sayfalar/Filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Türe göre ara

Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm türler alır bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList` Türlerin farklı türler yansıtArak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Razor Sayfasına türe göre arama ekleme

*Index.cshtml'i* aşağıdaki gibi güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: Sonraki sayfaları](xref:tutorials/razor-pages/da1)
> [güncelleştirme: Yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Aşağıdaki bölümlerde, *tür* e veya *ada* göre film arama sı eklenir.

*Sayfalar/Filmler/Index.cshtml.cs*aşağıdaki vurgulanan özellikleri ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: kullanıcıların arama metin kutusuna girdikleri metni içerir. `SearchString`[`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) özniteliği vardır. `[BindProperty]`değerleri ve sorgu dizelerini özellik ile aynı ada bağlar. `(SupportsGet = true)`GET isteklerini bağlamak için gereklidir.
* `Genres`: türlerin listesini içerir. `Genres`kullanıcının listeden bir tür seçmesine olanak tanır. `SelectList`Gerektirir`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: kullanıcının seçtiği özel türü içerir (örneğin, "Western").
* `Genres`ve `MovieGenre` daha sonra bu öğretici kullanılır.

[!INCLUDE[](~/includes/bind-get.md)]

Dizin sayfasının `OnGetAsync` yöntemini aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

`OnGetAsync` Yöntemin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgusu oluşturur:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**

`SearchString` Özellik boş veya boş değilse, film sorgusu arama dizesine filtre uygulayacak şekilde değiştirilir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kod `s => s.Title.Contains()` bir [Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)İfadesi. Lambdas, yöntem tabanlı [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) sorgularında [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metodu veya `Contains` (önceki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır. LINQ sorguları tanımlandıkları nda veya bir metodu `Where`(örneğin, `Contains` veya) `OrderBy`çağırArak değiştirildiğinde yürütülmez. Bunun yerine, sorgu yürütme ertelendi. Bu, bir ifadenin değerlendirilmesinin gerçekleşen değeri üzerinde tekrarlanana veya yöntem çağrılana `ToListAsync` kadar geciktiği anlamına gelir. Daha fazla bilgi için [Sorgu Yürütme'ye](/dotnet/framework/data/adonet/ef/language-reference/query-execution) bakın.

**Not:** [İçle metodu](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) C# kodunda değil, veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır. SQL Server'da, `Contains` sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)ile eşler, bu durum duyarsızdır. SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.

Filmler sayfasına gidin ve URL gibi `?searchString=Ghost` bir sorgu dizesini `https://localhost:5001/Movies?searchString=Ghost`ekleyin (örneğin, ). Filtre uygulanmış filmler görüntülenir.

![Dizin görünümü](search/_static/ghost.png)

Dizin sayfasına aşağıdaki rota şablonu eklenirse, arama dizesi BIR `https://localhost:5001/Movies/Ghost`URL kesimi olarak geçirilebilir (örneğin, ).

```cshtml
@page "{searchString?}"
```

Önceki rota kısıtlaması, başlığı sorgu dize değeri yerine rota verisi (URL kesimi) olarak aramayı sağlar.  In `?` `"{searchString?}"` bu isteğe bağlı bir rota parametresi olduğu anlamına gelir.

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](search/_static/g2.png)

ASP.NET Core çalışma süresi, `SearchString` sorgu dizesi ( ) veya`?searchString=Ghost`rota verilerinden`https://localhost:5001/Movies/Ghost`( ) özelliğin değerini ayarlamak için [model bağlama](xref:mvc/models/model-binding) kullanır. Model bağlama büyük/küçük harf duyarlı değildir.

Ancak, kullanıcıların bir film aramak için URL'yi değiştirmesini bekleyemezsiniz. Bu adımda, Film filtrelemek için UI eklenir. Rota kısıtlamasını `"{searchString?}"`eklediyseniz, kaldırın.

*Sayfalar/Filmler/Index.cshtml* dosyasını açın ve `<form>` aşağıdaki kodda vurgulanan biçimlendirmeyi ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

HTML `<form>` etiketi aşağıdaki [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanır:

* [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper). Form gönderildiğinde, filtre dizesi sorgu dizesi aracılığıyla *Sayfalar/Filmler/Dizin* sayfasına gönderilir.
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper)

Değişiklikleri kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](search/_static/filter.png)

## <a name="search-by-genre"></a>Türe göre ara

Yöntemi `OnGetAsync` aşağıdaki kodla güncelleştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, veritabanından tüm türler alır bir LINQ sorgusudur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList` Türlerin farklı türler yansıtArak oluşturulur.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Razor Sayfasına türe göre arama ekleme

*Index.cshtml'i* aşağıdaki gibi güncelleştirin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin.
Önceki kod, Tut Yardımcısı nı ve Seçenek Etiketi Yardımcısını [seçin'](xref:mvc/views/working-with-forms#the-select-tag-helper) i kullanır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Önceki: Sonraki sayfaları](xref:tutorials/razor-pages/da1)
> [güncelleştirme: Yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)

::: moniker-end
