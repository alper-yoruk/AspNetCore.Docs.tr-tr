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
ms.openlocfilehash: 652ee8c4bfe8980eb09246720f9ddf1743021f34
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399055"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a>Bölüm 7, ASP.NET Core MVC uygulamasına arama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde, `Index` film metoduna *tarz* veya *ada*göre arama özelliği ekleyebilirsiniz.

`Index` *Controllers/MoviesController. cs* içinde bulunan yöntemi aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

Eylem yönteminin ilk satırı, `Index` filmleri seçmek için bir [LINQ](/dotnet/standard/using-linq) sorgusu oluşturur:

```csharp
var movies = from m in _context.Movie
             select m;
```

Sorgu *yalnızca* bu noktada tanımlanmış, veritabanında çalıştırılmadı. **not**

`searchString`Parametresi bir dize içeriyorsa, filmler sorgusu arama dizesinin değerine göre filtrelenecek şekilde değiştirilir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

`s => s.Title.Contains()`Yukarıdaki kod bir [lambda ifadesidir](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdalar, Yöntem tabanlı [LINQ](/dotnet/standard/using-linq) sorgularında [WHERE](/dotnet/api/system.linq.enumerable.where) yöntemi veya `Contains` (Yukarıdaki kodda kullanılan) gibi standart sorgu işleci yöntemlerine bağımsız değişkenler olarak kullanılır. LINQ sorguları tanımlandıklarında veya, ya da gibi bir yöntem çağırarak değiştirildiklerinde yürütülmez `Where` `Contains` `OrderBy` . Bunun yerine sorgu yürütmesi ertelenir.  Diğer bir deyişle, bir ifadenin değerlendirmesi, gerçekleştirilmiş değeri gerçekten yineleneceği veya yöntem çağrılana kadar geciktirilen anlamına gelir `ToListAsync` . Ertelenmiş sorgu yürütme hakkında daha fazla bilgi için bkz. [sorgu yürütme](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Not: [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) yöntemi yukarıda gösterilen c# kodunda değil, veritabanında çalıştırılır. Sorgudaki büyük/küçük harf duyarlılığı veritabanına ve harmanlamaya bağlıdır. SQL Server üzerinde [SQL gibi](/sql/t-sql/language-elements/like-transact-sql)eşlemeler [içerir](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) , büyük/küçük harfe duyarsız olur. SQLite ' da, varsayılan harmanlama ile büyük/küçük harfe duyarlıdır.

`/Movies/Index` sayfasına gidin. URL 'ye gibi bir sorgu dizesi ekleyin `?searchString=Ghost` . Filtrelenmiş filmler görüntülenir.

![Dizin görünümü](~/tutorials/first-mvc-app/search/_static/ghost.png)

`Index`Yönteminin imzasını adlı bir parametreye sahip olacak şekilde değiştirirseniz `id` , `id` parametresi `{id}` *Startup.cs*içinde ayarlanan varsayılan yollar için isteğe bağlı yer tutucuya eşleşir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Parametresini `id` ve değişikliğin tüm oluşumlarını olarak değiştirin `searchString` `id` .

Önceki `Index` Yöntem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

`Index`Parametresi ile güncelleştirilmiş Yöntem `id` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Artık arama başlığını sorgu dizesi değeri yerine rota verileri (bir URL segmenti) olarak geçirebilirsiniz.

![URL 'ye hayalet sözcük eklenmiş olan dizin görünümü, Ghostbusters ve Ghostbusters ters ve 2 adet film listesi](~/tutorials/first-mvc-app/search/_static/g2.png)

Ancak, kullanıcıların bir filmi her arayışınızda URL 'YI değiştirmesini beklemeniz gerekmez. Böylece, filmlerin filtrelemesine yardımcı olmak için UI öğeleri ekleyeceğiz. `Index`Yol ile bağlantılı parametrenin nasıl geçirileceğini test etmek için yönteminin imzasını değiştirdiyseniz `ID` , adlı bir parametre alması için geri değiştirin `searchString` :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

*Views/filmler/Index. cshtml* dosyasını açın ve `<form>` aşağıda vurgulanan biçimlendirmeyi ekleyin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

HTML etiketi, form `<form>` [etiketi yardımcısını](xref:mvc/views/working-with-forms)kullanır, bu nedenle formu gönderdiğinizde, filtre dizesi `Index` film denetleyicisinin eylemine gönderilir. Değişikliklerinizi kaydedin ve sonra filtreyi test edin.

![Başlık filtresi metin kutusuna hayalet sözcük türü ile dizin görünümü](~/tutorials/first-mvc-app/search/_static/filter.png)

`[HttpPost]` `Index` Bekleneceğiniz gibi metodun aşırı yüklemesi yoktur. Bunun için gerekli değildir, çünkü yöntem uygulamanın durumunu değiştirmediğinden verileri filtrelememeniz yeterlidir.

Aşağıdaki `[HttpPost] Index` yöntemi ekleyebilirsiniz.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed`Parametresi, yöntemi için bir aşırı yükleme oluşturmak için kullanılır `Index` . Öğreticide daha sonra konuşacağız.

Bu yöntemi eklerseniz, Invoker `[HttpPost] Index` yöntemi yöntemiyle eşleşir ve `[HttpPost] Index` yöntemi aşağıdaki görüntüde gösterildiği gibi çalışır.

![HttpPost dizininden uygulama yanıtı olan tarayıcı penceresi: hayalet üzerinde filtrele](~/tutorials/first-mvc-app/search/_static/fo.png)

Ancak, bu yöntemin bu sürümünü eklemeseniz bile `[HttpPost]` `Index` , tümünün nasıl uygulandığını gösteren bir sınırlama vardır. Belirli bir arama için yer işareti koymak istediğinizi veya aynı film filtrelenmiş listesini görmek için onlara tıklabilecekleri bir bağlantı göndermek istediğinizi düşünün. HTTP POST isteğinin URL 'SI GET isteğinin URL 'siyle (localhost: {PORT}/filmler/dizin) aynı olduğunu fark edin; URL 'de arama bilgisi yok. Arama dizesi bilgileri sunucuya [form alanı değeri](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data)olarak gönderilir. Tarayıcı geliştirici araçları veya harika [Fiddler aracının](https://www.telerik.com/fiddler)olduğunu doğrulayabilirsiniz. Aşağıdaki görüntüde Chrome tarayıcı geliştirici araçları gösterilmektedir:

![Microsoft Edge 'de Geliştirici Araçları, bir searchString değeri hayalet olan bir istek gövdesini gösteren ağ sekmesi](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Arama parametresini ve [XSRF](xref:security/anti-request-forgery) belirtecini istek gövdesinde görebilirsiniz. Bu şekilde, önceki öğreticide bahsedildiği gibi, [form etiketi Yardımcısı](xref:mvc/views/working-with-forms) , bir [XSRF](xref:security/anti-request-forgery) Anti-forgery belirteci oluşturur. Verileri değiştiriyoruz, bu nedenle denetleyiciyi denetleyici yönteminde doğrulamamız gerekmiyor.

Arama parametresi, URL değil, istek gövdesinde olduğundan, bu arama bilgilerini, yer işareti veya başkalarıyla paylaşmak için yakalayamazsınız. İsteğin, `HTTP GET` *Görünümler/filmler/Index. cshtml* dosyasında bulunması gerektiğini belirterek bunu düzeltemedi.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Artık bir arama gönderdiğinizde, URL arama sorgu dizesini içerir. `HttpGet Index`Bir yönteminiz olsa da, arama eylem yöntemine de gidecektir `HttpPost Index` .

![URL 'de searchString = hayalet ve döndürülen Filmler, Ghostbusters ve Ghostbusters 'ler 2 olan tarayıcı penceresi, hayalet sözcüğünü içerir](~/tutorials/first-mvc-app/search/_static/search_get.png)

Aşağıdaki biçimlendirme etiketine olan değişikliği gösterir `form` :

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Türe göre arama Ekle

`MovieGenreViewModel` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Film tarzı görünüm modeli şunları içerir:

* Bir film listesi.
* `SelectList`Tarzlar listesini içeren bir. Bu, kullanıcının listeden bir tarz seçmesine olanak sağlar.
* `MovieGenre`, seçilen tarzı içeren.
* `SearchString`, kullanıcılar arama metin kutusuna girdiğiniz metni içerir.

`Index`İçindeki yöntemini `MoviesController.cs` aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Aşağıdaki kod, `LINQ` veritabanından tüm tarzları alan bir sorgudur.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList`Tarzlar ayrı tarzlar yansıtıyor (Select listenizin yinelenen tarzlar olmasını istemiyorum).

Kullanıcı öğeyi aradığında arama değeri arama kutusuna tutulur.

## <a name="add-search-by-genre-to-the-index-view"></a>Tarzı, dizin görünümüne göre ara ekleme

`Index.cshtml`Şu şekilde *görünümlerde/filmlerde* bulunan güncelleştirme:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

Yukarıdaki kodda, `DisplayNameFor` HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler. Lambda ifadesi değerlendirilmek yerine incelenebileceğinden,, `model` `model.Movies` , veya boş olduğunda bir erişim ihlali almazsınız `model.Movies[0]` `null` . Lambda ifadesi değerlendirildiğinde (örneğin, `@Html.DisplayFor(modelItem => item.Title)` ), modelin özellik değerleri değerlendirilir.

Türe göre, film başlığına göre ve her ikisine birden arayarak uygulamayı test edin:

![Sonuçları gösteren tarayıcı penceresihttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Önceki](controller-methods-views.md) 
>  [Sonraki](new-field.md)
