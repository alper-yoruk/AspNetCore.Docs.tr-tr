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
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına arama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde, *türveya* *ada*göre film arama yapmanızı sağlayan eylem yöntemine `Index` arama yeteneği eklersiniz.

`Index` *Denetleyiciler/MoviesController.cs* içinde bulunan yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

`Index` Eylem yönteminin ilk satırı filmleri seçmek için bir [LINQ](/dotnet/standard/using-linq) sorgusu oluşturur:

```csharp
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanır, veritabanına karşı **çalıştırılmadı.**

`searchString` Parametre bir dize içeriyorsa, film sorgusu arama dizesinin değerini filtrelemek için değiştirilir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Yukarıdaki `s => s.Title.Contains()` kod [lambda ifadesidir.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) Lambdas, yöntem tabanlı [LINQ](/dotnet/standard/using-linq) sorgularında [Where](/dotnet/api/system.linq.enumerable.where) metodu veya `Contains` (yukarıdaki kodda kullanılır) gibi standart sorgu işleci yöntemlerine bağımsız değişken olarak kullanılır. LINQ sorguları tanımlandıkları nda veya değiştirildiğinde `Where`, yani . `Contains` `OrderBy` Bunun yerine, sorgu yürütme ertelendi.  Bu, bir ifadenin değerlendirilmesinin, gerçekleşen değeri gerçekten üzerinde yinelenene veya yöntem çağrılına `ToListAsync` kadar geciktiği anlamına gelir. Ertelenmiş sorgu yürütmesi hakkında daha fazla bilgi için sorgu [yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution)bölümüne bakın.

Not: [İçerme](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi, yukarıda gösterilen c# kodunda değil, veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlama bağlıdır. SQL Server'da, sql [LIKE](/sql/t-sql/language-elements/like-transact-sql)için haritalar [içerir,](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) bu da büyük/küçük harf duyarsızdır. SQLite'da, varsayılan harmanlama ile, bu durumda duyarlı.

`/Movies/Index` sayfasına gidin. URL gibi `?searchString=Ghost` bir sorgu dizesini ekleyin. Filtre uygulanmış filmler görüntülenir.

![Dizin görünümü](~/tutorials/first-mvc-app/search/_static/ghost.png)

Yöntemin `Index` imzasını bir parametre olarak `id`değiştirirseniz, `id` parametre *Startup.cs* `{id}` ayarlanan varsayılan yollar için isteğe bağlı yer tutucuyla eşleşir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Parametreyi `id` ve tüm `searchString` değişiklik oluşumlarını `id`' ya çevirin.

Önceki `Index` yöntem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Parametre `Index` ile `id` güncelleştirilmiş yöntem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Artık arama başlığını sorgu dizesi değeri yerine rota verisi (URL segmenti) olarak geçirebilirsiniz.

![Url'ye eklenen hayalet sözcüğü ile dizin görünümü ve ghostbusters ve Ghostbusters 2 olmak üzere iki filmden oluşan iade edilen bir film listesi](~/tutorials/first-mvc-app/search/_static/g2.png)

Ancak, kullanıcıların bir film aramak istediklerinde URL'yi değiştirmelerini bekleyemezsiniz. Şimdi filmleri filtrelemelerine yardımcı olmak için Ara Birimi öğelerini eklersiniz. Rotaya bağlı `Index` `ID` parametrenin nasıl geçeceğini test etmek için yöntemin imzasını değiştirdiyseniz, adı `searchString`verilen bir parametre yi kapacak şekilde değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

*Görünümler/Filmler/Index.cshtml* dosyasını açın `<form>` ve aşağıda vurgulanan biçimlendirmeyi ekleyin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

HTML `<form>` etiketi [Form Tag Helper](xref:mvc/views/working-with-forms)kullanır, böylece formu gönderdiğinde, filtre `Index` dizesi film denetleyicisinin eylemine nakledilir. Değişikliklerinizi kaydedin ve filtreyi test edin.

![Başlık filtresi metin kutusuna yazılan hayalet sözcüğüyle dizin görünümü](~/tutorials/first-mvc-app/search/_static/filter.png)

Tahmin edebileceğiniz `[HttpPost]` gibi `Index` yöntemin aşırı yüklenmez. Yöntem uygulamanın durumunu değiştirmediği için buna ihtiyacınız yok, yalnızca verileri filtreleme.

Aşağıdaki `[HttpPost] Index` yöntemi ekleyebilirsiniz.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` Parametre `Index` yöntem için bir aşırı yük oluşturmak için kullanılır. Bunu daha sonra öğreticide konuşuruz.

Bu yöntemi eklerseniz, eylem çağıran `[HttpPost] Index` yöntem eşleşir `[HttpPost] Index` ve yöntem aşağıdaki resimde gösterildiği gibi çalışır.

![HttpPost Index uygulama yanıtı ile Tarayıcı penceresi: hayalet filtre](~/tutorials/first-mvc-app/search/_static/fo.png)

Ancak, `Index` yöntemin bu `[HttpPost]` sürümünü ekleseniz bile, tüm bunların nasıl uygulandığı konusunda bir sınırlama vardır. Belirli bir aramayı yer imi yapmak istediğinizi veya aynı filtreuygulanmış film listesini görmek için tıklatabilecekleri arkadaşlarınıza bir bağlantı göndermek istediğinizi düşünün. HTTP POST isteğinin URL'sinin GET isteğinin URL'si ile aynı olduğuna dikkat edin (localhost:{PORT}/Filmler/Dizini) - URL'de arama bilgisi yok. Arama dizesi bilgileri [bir form alanı değeri](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)olarak sunucuya gönderilir. Bunu tarayıcı Geliştirici araçları veya mükemmel [Fiddler aracı](https://www.telerik.com/fiddler)ile doğrulayabilirsiniz. Aşağıdaki resimde Chrome tarayıcı geliştirici araçları gösterilmektedir:

![Microsoft Edge'deki Geliştirici Araçları'nın ağ sekmesi, bir istek gövdesini hayaletin searchString değerine sahip gösteren](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

İstek gövdesinde arama parametresini ve [XSRF](xref:security/anti-request-forgery) belirtecinizi görebilirsiniz. Not, önceki öğreticide belirtildiği gibi, [Form Tag Helper](xref:mvc/views/working-with-forms) bir [XSRF](xref:security/anti-request-forgery) anti-sahtebelirteci oluşturur. Verileri değiştirmiyoruz, bu nedenle denetleyici yönteminde belirteçleri doğrulamamız gerekmez.

Arama parametresi URL'de değil, istek gövdesinde olduğundan, bu arama bilgilerini yer imi yapmak veya başkalarıyla paylaşmak için yakalayamadığınıziçin. İstek belirterek düzeltin `HTTP GET` *Görünümler/Filmler/Index.cshtml* dosyasında bulunmalıdır.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Şimdi bir arama gönderdiğinde, URL arama sorgu dizesini içerir. Arama, bir `HttpGet Index` `HttpPost Index` yönteminiz olsa bile eylem yöntemine de gider.

![Tarayıcı penceresi aramaString = Url hayalet gösteren ve filmler döndü, Ghostbusters ve Ghostbusters 2, kelime hayalet içeren](~/tutorials/first-mvc-app/search/_static/search_get.png)

Aşağıdaki biçimlendirme `form` etiketteki değişikliği gösterir:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Türe göre Arama Ekle

`MovieGenreViewModel` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Film türü görünümü modeli şunları içerecektir:

* Filmlerin listesi.
* Türlerin `SelectList` listesini içeren bir. Bu, kullanıcının listeden bir tür seçmesine olanak tanır.
* `MovieGenre`, seçili türü içerir.
* `SearchString`, kullanıcıların arama metin kutusuna girdikleri metni içerir.

Yöntemi `Index` aşağıdaki `MoviesController.cs` kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, `LINQ` veritabanından tüm türler alır bir sorgudur.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

Türler, `SelectList` farklı türler yansıtılarak oluşturulur (seçili listemizin yinelenen türlere sahip olmasını istemeyiz).

Kullanıcı öğeyi aradığında, arama değeri arama kutusunda tutulur.

## <a name="add-search-by-genre-to-the-index-view"></a>Dizin görünümüne türe göre arama ekleme

`Index.cshtml` *Görünümler/Filmler/* aşağıdaki gibi bulunan güncelleştirme:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

Önceki kodda, `DisplayNameFor` HTML Yardımcısı görüntü adını `Title` belirlemek için lambda ifadesinde başvurulan özelliği inceler. Lambda `model`ifadesi değerlendirilmek yerine denetlendiğinden, "veya `model.Movies` `model.Movies[0]` `null` boş" olduğunda bir erişim ihlali almazsınız. Lambda ifadesi değerlendirildiğinde (örneğin), `@Html.DisplayFor(modelItem => item.Title)`modelin özellik değerleri değerlendirilir.

Uygulamayı türe, film başlığına ve her ikisine göre arayarak test edin:

![Sonuçlarını gösteren tarayıcı penceresihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Önceki](controller-methods-views.md)
> [Sonraki](new-field.md)
