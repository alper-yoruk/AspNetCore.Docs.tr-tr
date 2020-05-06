---
title: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme hakkında bilgi edinin.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: d9ab20b7ed4b394c154141efe3a94481efaf063c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774554"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir. **ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu Güncelleştir

*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklaması, Entity Framework Core veritabanındaki para birimiyle `Price` doğru şekilde eşlenmesine olanak sağlar. Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir. [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.

Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

**Düzenle**, **Ayrıntılar**ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun Razor dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar. `AnchorTagHelper` Yukarıdaki KODDA, HTML `href` öznitelik değerini Razor sayfadan dinamik olarak oluşturur (yol göreli olur), `asp-page`, ve yol kimliği (`asp-route-id`). Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .

Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın. Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle (örneğin, `?id=1` içinde `https://localhost:5001/Movies/Details?id=1`) iletir.

### <a name="add-route-template"></a>Rota şablonu Ekle

"{İd: int}" yol Razor şablonunu kullanmak için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin. Bu sayfaların her biri için Page yönergesini ' den `@page` ' e `@page "{id:int}"`değiştirin. Uygulamayı çalıştırın ve kaynağı görüntüleyin. Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor. Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür. KIMLIĞI isteğe bağlı yapmak için yol kısıtlamasına `?` ekleyin:

 ```cshtml
@page "{id:int?}"
```

Davranışını test etmek için `@page "{id:int?}"`:

* *Pages/filmler/details. cshtml* içindeki Page yönergesini olarak `@page "{id:int?}"`ayarlayın.
* İçinde `public async Task<IActionResult> OnGetAsync(int? id)` bir kesme noktası ayarlayın ( *sayfalarda/filmlerde/details. cshtml. cs*).
* `https://localhost:5001/Movies/Details/` sayfasına gidin.

`@page "{id:int}"` Yönergeyle, kesme noktası hiçbir şekilde vurılmaz. Yönlendirme Altyapısı HTTP 404 döndürür. Kullanarak `@page "{id:int?}"`, `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.

`catch` Bloğu test etmek için:

* Üzerinde bir kesme noktası ayarlayın`catch (DbUpdateConcurrencyException)`
* Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.
* Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.
* Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Gönderme ve bağlama incelemesi

*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.
* `Page` Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler. *Pages/filmler/Edit. cshtml* dosyası, film modelinin sayfada kullanılabilir olmasını`@model RazorPagesMovie.Pages.Movies.EditModel`sağlayan model yönergesini () içerir.
* Düzenleme formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Düzenle sayfası gönderildiğinde:

* Sayfadaki form değerleri `Movie` özelliğine bağlıdır. Öznitelik `[BindProperty]` , [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle yeniden görüntülenir.
* Model hatası yoksa, film kaydedilir.

Dizin, oluşturma ve silme Razor SAYFALARıNDA http get yöntemleri benzer bir düzene uyar. Oluşturma `OnPostAsync` Razor sayfasındaki http post yöntemi, düzenleme `OnPostAsync` Razor sayfasındaki yöntemine benzer bir düzen izler.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: bir veritabanıyla](xref:tutorials/razor-pages/sql)
> çalışma[Sonraki: Arama ekle](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir. **ReleaseDate** **Yayın tarihi** (iki sözcük) olmalıdır.

![Chrome 'da açık film uygulaması](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu Güncelleştir

*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklaması, Entity Framework Core veritabanındaki para birimiyle `Price` doğru şekilde eşlenmesine olanak sağlar. Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir. [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.

Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

**Düzenle**, **Ayrıntılar**ve **Sil** bağlantıları, *Sayfalar/filmler/Index. cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun Razor dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar. `AnchorTagHelper` Yukarıdaki KODDA, HTML `href` öznitelik değerini Razor sayfadan dinamik olarak oluşturur (yol göreli olur), `asp-page`, ve yol kimliği (`asp-route-id`). Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .

Oluşturulan biçimlendirmeyi incelemek için sık kullandığınız tarayıcıdan **Görünüm kaynağını** kullanın. Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle (örneğin, `?id=1` içinde `https://localhost:5001/Movies/Details?id=1`) iletir.

"{İd: int}" yol Razor şablonunu kullanmak için düzenleme, Ayrıntılar ve silme sayfalarını güncelleştirin. Bu sayfaların her biri için Page yönergesini ' den `@page` ' e `@page "{id:int}"`değiştirin. Uygulamayı çalıştırın ve kaynağı görüntüleyin. Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor. Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürür. KIMLIĞI isteğe bağlı yapmak için yol kısıtlamasına `?` ekleyin:

 ```cshtml
@page "{id:int?}"
```

Davranışını test etmek için `@page "{id:int?}"`:

* *Pages/filmler/details. cshtml* içindeki Page yönergesini olarak `@page "{id:int?}"`ayarlayın.
* İçinde `public async Task<IActionResult> OnGetAsync(int? id)` bir kesme noktası ayarlayın ( *sayfalarda/filmlerde/details. cshtml. cs*).
* `https://localhost:5001/Movies/Details/` sayfasına gidin.

`@page "{id:int}"` Yönergeyle, kesme noktası hiçbir şekilde vurılmaz. Yönlendirme Altyapısı HTTP 404 döndürür. Kullanarak `@page "{id:int?}"`, `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.

`catch` Bloğu test etmek için:

* Üzerinde bir kesme noktası ayarlayın`catch (DbUpdateConcurrencyException)`
* Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet**' i girmeyin.
* Başka bir tarayıcı penceresinde, aynı filmin **Sil** bağlantısını seçin ve ardından filmi silin.
* Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Gönderme ve bağlama incelemesi

*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında (örneğin, `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür. 
* `Page` Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler. *Pages/filmler/Edit. cshtml* dosyası, film modelinin sayfada kullanılabilir olmasını`@model RazorPagesMovie.Pages.Movies.EditModel`sağlayan model yönergesini () içerir.
* Düzenleme formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Düzenle sayfası gönderildiğinde:

* Sayfadaki form değerleri `Movie` özelliğine bağlıdır. Öznitelik `[BindProperty]` , [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle birlikte görüntülenir.
* Model hatası yoksa, film kaydedilir.

Dizin, oluşturma ve silme Razor SAYFALARıNDA http get yöntemleri benzer bir düzene uyar. Oluşturma `OnPostAsync` Razor sayfasındaki http post yöntemi, düzenleme `OnPostAsync` Razor sayfasındaki yöntemine benzer bir düzen izler.

Arama sonraki öğreticiye eklenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Önceki: bir veritabanıyla](xref:tutorials/razor-pages/sql)
> çalışma[Sonraki: Arama ekle](xref:tutorials/razor-pages/search)

::: moniker-end
