---
title: 5. kısım, oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: Sayfalardaki eğitim serisinin 5. bölümü Razor .
ms.author: riande
ms.date: 09/20/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 7146c1955a578502a63578de4f1abce932cb8b32
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360614"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>5. bölüm, ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir. **ReleaseDate** iki sözcük, **Yayın tarihi** olmalıdır.

![Chrome 'da açık film uygulaması](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu Güncelleştir

*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Önceki kodda:

* `[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` . Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).
* [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) özniteliği bir alanın görünen adını belirtir. Yukarıdaki kodda, "ReleaseDate" yerine "Yayın tarihi".
* [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği, veri türünü belirtir ( `Date` ). Alanda depolanan zaman bilgileri gösterilmez.

[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır.

Hedef URL 'yi görmek için *sayfalara/filmlere* gidin ve bir **düzenleme** bağlantısının üzerine gelin.

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si https://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

**Düzenle** , **Ayrıntılar** ve bağlantılar, **Delete** *Sayfalar/filmler/ Index . cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .

Yukarıdaki kodda, [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , sayfada HTML öznitelik değerini dinamik olarak oluşturur `href` Razor (yol göreli olur),, `asp-page` ve yol tanımlayıcısı ( `asp-route-id` ). Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages).

Oluşturulan biçimlendirmeyi incelemek için bir tarayıcıdan **Görünüm kaynağı** ' nı kullanın. Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle iletir. Örneğin, `?id=1` içinde `https://localhost:5001/Movies/Details?id=1` .

### <a name="add-route-template"></a>Rota şablonu Ekle

Delete Razor Yol şablonunu kullanmak için düzenleme, Ayrıntılar ve sayfaları güncelleştirin `{id:int}` . Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` . Uygulamayı çalıştırın ve kaynağı görüntüleyin.

Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayı içermeyen yol şablonuna sahip sayfaya yönelik bir istek, `{id:int}` HTTP **not** 404 (bulunamadı) hatası döndürür. Örneğin, `https://localhost:5001/Movies/Details` bir 404 hatası döndürür. KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:

```cshtml
@page "{id:int?}"
```

Davranışını test edin `@page "{id:int?}"` :

1. *Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .
1. `public async Task<IActionResult> OnGetAsync(int? id)` *Sayfalarda/filmlerde/details. cshtml. cs* ' de bir kesme noktası ayarlayın.
1. `https://localhost:5001/Movies/Details/` sayfasına gidin.

`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz. Yönlendirme Altyapısı HTTP 404 döndürür. Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.

Bloğu test etmek için `catch` :

1. Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)` .
1. Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet** ' i girmeyin.
1. Başka bir tarayıcı penceresinde, **Delete** aynı filmin bağlantısını seçin ve ardından filmi silin.
1. Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Gönderme ve bağlama incelemesi

*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında, örneğin `https://localhost:5001/Movies/Edit/3` :

* `OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür.
* `Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler. *Pages/filmler/Edit. cshtml* dosyası model yönergesini içerir `@model RazorPagesMovie.Pages.Movies.EditModel` , bu da film modelini sayfada kullanılabilir hale getirir.
* Düzenleme formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Düzenle sayfası gönderildiğinde:

* Sayfadaki form değerleri `Movie` özelliğine bağlıdır. `[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa, örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa, form gönderilen değerlerle birlikte görüntülenir.
* Model hatası yoksa, film kaydedilir.

, Ve sayfalarındaki HTTP GET metotları Index Create Delete Razor benzer bir düzene uyar. Sayfadaki HTTP POST `OnPostAsync` yöntemi, Create Razor düzenleme sayfasındaki yöntemine benzer bir düzen izler `OnPostAsync` Razor .

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Yapı iskelesi film uygulamasının iyi bir başlangıcı vardır ancak sunum ideal değildir. **ReleaseDate** iki sözcük, **Yayın tarihi** olmalıdır.

![Chrome 'da açık film uygulaması](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu Güncelleştir

*Modeller/film. cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanmış satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]`Veri ek açıklaması, Entity Framework Core veritabanındaki para birimiyle doğru şekilde eşlenmesine olanak sağlar `Price` . Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

[Veri açıklamaları](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğreticide ele alınmıştır. [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) özniteliği, "ReleaseDate" yerine "Yayın tarihi" Bu örnekte, bir alanın adı için nelerin gösterileceğini belirtir. [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ), bu nedenle alanda depolanan zaman bilgileri gösterilmez.

Hedef URL 'yi görmek için sayfalara/filmlere gidin ve bir **düzenleme** bağlantısının üzerine gelin.

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si http://localhost:1234/Movies/Edit/5 gösteriliyor](~/tutorials/razor-pages/da1/edit7.png)

**Düzenle** , **Ayrıntılar** ve bağlantılar, **Delete** *Sayfalar/filmler/ Index . cshtml* dosyasındaki [tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor . Yukarıdaki kodda, `AnchorTagHelper` HTML öznitelik değerini sayfadan dinamik olarak oluşturur `href` Razor (yol göreli olur),, `asp-page` ve yol kimliği ( `asp-route-id` ). Daha fazla bilgi için bkz. [Sayfalar Için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) .

Oluşturulan biçimlendirmeyi incelemek için bir tarayıcıdan **Görünüm kaynağı** ' nı kullanın. Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dinamik olarak oluşturulan bağlantılar film KIMLIĞINI bir sorgu dizesiyle iletir. Örneğin, `?id=1` içinde  `https://localhost:5001/Movies/Details?id=1` .

Delete Razor "{İd: int}" yol şablonunu kullanmak için düzenleme, Ayrıntılar ve sayfaları güncelleştirin. Bu sayfaların her biri için Page yönergesini ' den ' `@page` e değiştirin `@page "{id:int}"` . Uygulamayı çalıştırın ve kaynağı görüntüleyin. Oluşturulan HTML, URL 'nin yol bölümüne KIMLIĞI ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayıyı **içermeyen "** {id: int}" yol şablonuna sahip sayfaya yönelik bir Istek, HTTP 404 (bulunamadı) hatası döndürüyor. Örneğin, `https://localhost:5001/Movies/Details` bir 404 hatası döndürür. KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:

 ```cshtml
@page "{id:int?}"
```

Davranışını test etmek için `@page "{id:int?}"` :

* *Pages/filmler/details. cshtml* içindeki Page yönergesini olarak ayarlayın `@page "{id:int?}"` .
* `public async Task<IActionResult> OnGetAsync(int? id)` *Sayfalarda/filmlerde/details. cshtml. cs* ' de bir kesme noktası ayarlayın.
* `https://localhost:5001/Movies/Details/` sayfasına gidin.

`@page "{id:int}"`Yönergeyle, kesme noktası hiçbir şekilde vurılmaz. Yönlendirme Altyapısı HTTP 404 döndürür. Kullanarak `@page "{id:int?}"` , `OnGetAsync` yöntemi döndürür `NotFound` (HTTP 404):

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Pages/filmler/Edit. cshtml. cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiği ve diğer istemci filmle değişiklik yaptığı zaman eşzamanlılık özel durumlarını algılar.

Bloğu test etmek için `catch` :

* Üzerinde bir kesme noktası ayarlayın `catch (DbUpdateConcurrencyException)`
* Film için **Düzenle** ' yi seçin, değişiklikler yapın, ancak **Kaydet** ' i girmeyin.
* Başka bir tarayıcı penceresinde, **Delete** aynı filmin bağlantısını seçin ve ardından filmi silin.
* Önceki tarayıcı penceresinde filmdeki değişiklikleri gönderin.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Daha fazla bilgi için bkz. [eşzamanlılık çakışmalarını işleme](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Gönderme ve bağlama incelemesi

*Pages/filmler/Edit. cshtml. cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Filmler/düzenleme sayfasına HTTP GET isteği yapıldığında, örneğin `https://localhost:5001/Movies/Edit/3` :

* `OnGetAsync`Yöntemi, filmi veritabanından getirir ve `Page` yöntemi döndürür. 
* `Page`Yöntemi *Sayfalar/filmler/Edit. cshtml* Razor sayfasını işler. *Pages/filmler/Edit. cshtml* dosyası model yönergesini içerir `@model RazorPagesMovie.Pages.Movies.EditModel` , bu da film modelini sayfada kullanılabilir hale getirir.
* Düzenleme formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Düzenle sayfası gönderildiğinde:

* Sayfadaki form değerleri `Movie` özelliğine bağlıdır. `[BindProperty]`Öznitelik, [model bağlamayı](xref:mvc/models/model-binding)mümkün bir şekilde sunar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa, örneğin, `ReleaseDate` bir tarihe dönüştürülemiyorsa, form gönderilen değerlerle birlikte görüntülenir.
* Model hatası yoksa, film kaydedilir.

, Ve sayfalarındaki HTTP GET metotları Index Create Delete Razor benzer bir düzene uyar. Sayfadaki HTTP POST `OnPostAsync` yöntemi, Create Razor düzenleme sayfasındaki yöntemine benzer bir düzen izler `OnPostAsync` Razor .

Arama sonraki öğreticiye eklenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Önceki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql) 
>  [Sonraki: Arama ekle](xref:tutorials/razor-pages/search)

::: moniker-end
