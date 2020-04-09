---
title: ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında oluşturulan sayfaları nasıl güncelleştirtini öğrenin.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666217"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>ASP.NET Core uygulamasında oluşturulan sayfaları güncelleştirme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

İskele film uygulaması iyi bir başlangıç vardır, ancak sunum ideal değildir. **Yayın Tarihi** **Çıkış Tarihi** (iki sözcük) olmalıdır.

![Chrome'da film uygulaması açıldı](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu güncelleştirme

*Modeller/Movie.cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanan satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklama, Entity Framework Core'un `Price` veritabanındaki para birimine doğru şekilde eşlemesini sağlar. Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğretici de ele alınmıştır. [Ekran](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği, bir alanın adı için ne görüntüleneceklerini belirtir (bu durumda "Yayın Tarihi" yerine "Yayın Tarihi" ). [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü (Tarih) belirtir, böylece alanda depolanan saat bilgileri görüntülenmez.

Sayfalara/Filmlere göz atın ve hedef URL'yi görmek için bir **Edit** bağlantısının üzerine tıklayın.

![Edit bağlantısı üzerinde fare ile tarayıcı penceresi http://localhost:1234/Movies/Edit/5 ve bir bağlantı Url gösterilir](~/tutorials/razor-pages/da1/edit7.png)

**Düzenleme,** **Ayrıntılar**ve **Sil** *bağlantıları, Sayfalar/Filmler/Index.cshtml* dosyasındaki [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir. `AnchorTagHelper` Önceki kodda, dinamik olarak Razor Page `href` (rota göreceli), `asp-page`ve rota id ( HTML`asp-route-id`öznitelik değeri oluşturur. Daha fazla bilgi [için Sayfalar için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) adresine bakın.

Oluşturulan biçimlendirmeyi incelemek için favori tarayıcınızdan **Kaynak Görünümü'nu** kullanın. Oluşturulan HTML'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dinamik olarak oluşturulan bağlantılar film kimliğini sorgu dizesiyle (örneğin, `?id=1` giriş) `https://localhost:5001/Movies/Details?id=1`geçirir.

### <a name="add-route-template"></a>Rota şablonu ekleme

"{id:int}" rota şablonunu kullanmak için Düzenle, Ayrıntıları ve Jilet Sayfalarını Sil'i güncelleştirin. Bu sayfaların her biri için `@page` `@page "{id:int}"`sayfa yönergesini ' den ' e değiştirin Uygulamayı çalıştırın ve kaynağı görüntüleyin. Oluşturulan HTML, kimliği URL'nin yol bölümüne ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayı içermeyen "{id:int}" rota şablonuna **not** sahip sayfaya gelen bir istek, http 404 (bulunamadı) hatasını döndürecektir. Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürecek. Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:

 ```cshtml
@page "{id:int?}"
```

Davranışını test `@page "{id:int?}"`etmek için:

* *Sayfalar/Filmler/Details.cshtml'deki* sayfa yönergesini `@page "{id:int?}"`.
* Bir kesme noktası `public async Task<IActionResult> OnGetAsync(int? id)` ayarlayın *(Sayfalar/Filmler/Ayrıntılar.cshtml.cs*içinde).
* `https://localhost:5001/Movies/Details/` sayfasına gidin.

Direktifle, `@page "{id:int}"` kırılma noktası asla vurulmaz. Yönlendirme motoru HTTP 404 döndürür. Kullanarak, `@page "{id:int?}"` `OnGetAsync` yöntem `NotFound` döndürür (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Sayfalar/Filmler/Edit.cshtml.cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmdeki değişiklikleri yayınladığında eşzamanlılık özel durumlarını algılar.

`catch` Bloğu test etmek için:

* Kesme noktasını ayarlama`catch (DbUpdateConcurrencyException)`
* Film için **Edit'i** seçin, değişiklik yapın, ancak **Kaydet'i**girmeyin.
* Başka bir tarayıcı penceresinde, aynı film için **Sil** bağlantısını seçin ve ardından filmi silin.
* Önceki tarayıcı penceresinde, filmdeki değişiklikleri yayınlayın.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Bkz. Daha fazla bilgi için [eşzamanlılık çakışmalarını ele alın.](xref:data/ef-rp/concurrency)

### <a name="posting-and-binding-review"></a>Gönderme ve bağlayıcı inceleme

*Sayfalar/Filmler/Edit.cshtml.cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Filmler/Edit sayfasına http GET isteği yapıldığında (örneğin,): `http://localhost:5000/Movies/Edit/2`

* Yöntem, `OnGetAsync` filmi veritabanından getirir ve `Page` yöntemi döndürür.
* Yöntem, `Page` *Sayfaları/Filmleri/Edit.cshtml* Jilet Sayfasını işler. *Sayfalar/Filmler/Edit.cshtml* dosyası, film modelini sayfada kullanılabilir kılan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`), içerir.
* Edit formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Edit sayfası yayınlandığında:

* Sayfadaki form değerleri `Movie` özelliğe bağlıdır. Öznitelik `[BindProperty]` Model [bağlama](xref:mvc/models/model-binding)sağlar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle yeniden görüntülenir.
* Model hatası yoksa, film kaydedilir.

Index, Create ve Delete Razor sayfalarındaki HTTP GET yöntemleri de benzer bir örüntü izler. Jilet `OnPostAsync` Oluştur Sayfasındaki HTTP POST yöntemi, `OnPostAsync` Razor Sayfasını Düzenle'deki yönteme benzer bir örüntü izler.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Veritabanıyla](xref:tutorials/razor-pages/sql)
> Çalışma[Sonraki: Arama ekleme](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

İskele film uygulaması iyi bir başlangıç vardır, ancak sunum ideal değildir. **Yayın Tarihi** **Çıkış Tarihi** (iki sözcük) olmalıdır.

![Chrome'da film uygulaması açıldı](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Oluşturulan kodu güncelleştirme

*Modeller/Movie.cs* dosyasını açın ve aşağıdaki kodda gösterilen vurgulanan satırları ekleyin:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Veri `[Column(TypeName = "decimal(18, 2)")]` ek açıklama, Entity Framework Core'un `Price` veritabanındaki para birimine doğru şekilde eşlemesini sağlar. Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) sonraki öğretici de ele alınmıştır. [Ekran](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği, bir alanın adı için ne görüntüleneceklerini belirtir (bu durumda "Yayın Tarihi" yerine "Yayın Tarihi" ). [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü (Tarih) belirtir, böylece alanda depolanan saat bilgileri görüntülenmez.

Sayfalara/Filmlere göz atın ve hedef URL'yi görmek için bir **Edit** bağlantısının üzerine tıklayın.

![Edit bağlantısı üzerinde fare ile tarayıcı penceresi http://localhost:1234/Movies/Edit/5 ve bir bağlantı Url gösterilir](~/tutorials/razor-pages/da1/edit7.png)

**Düzenleme,** **Ayrıntılar**ve **Sil** *bağlantıları, Sayfalar/Filmler/Index.cshtml* dosyasındaki [Çapa Etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) tarafından oluşturulur.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir. `AnchorTagHelper` Önceki kodda, dinamik olarak Razor Page `href` (rota göreceli), `asp-page`ve rota id ( HTML`asp-route-id`öznitelik değeri oluşturur. Daha fazla bilgi [için Sayfalar için URL oluşturma](xref:razor-pages/index#url-generation-for-pages) adresine bakın.

Oluşturulan biçimlendirmeyi incelemek için favori tarayıcınızdan **Kaynak Görünümü'nu** kullanın. Oluşturulan HTML'nin bir bölümü aşağıda gösterilmiştir:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dinamik olarak oluşturulan bağlantılar film kimliğini sorgu dizesiyle (örneğin, `?id=1` giriş) `https://localhost:5001/Movies/Details?id=1`geçirir.

"{id:int}" rota şablonunu kullanmak için Düzenle, Ayrıntıları ve Jilet Sayfalarını Sil'i güncelleştirin. Bu sayfaların her biri için `@page` `@page "{id:int}"`sayfa yönergesini ' den ' e değiştirin Uygulamayı çalıştırın ve kaynağı görüntüleyin. Oluşturulan HTML, kimliği URL'nin yol bölümüne ekler:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Tamsayı içermeyen "{id:int}" rota şablonuna **not** sahip sayfaya gelen bir istek, http 404 (bulunamadı) hatasını döndürecektir. Örneğin, `http://localhost:5000/Movies/Details` bir 404 hatası döndürecek. Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:

 ```cshtml
@page "{id:int?}"
```

Davranışını test `@page "{id:int?}"`etmek için:

* *Sayfalar/Filmler/Details.cshtml'deki* sayfa yönergesini `@page "{id:int?}"`.
* Bir kesme noktası `public async Task<IActionResult> OnGetAsync(int? id)` ayarlayın *(Sayfalar/Filmler/Ayrıntılar.cshtml.cs*içinde).
* `https://localhost:5001/Movies/Details/` sayfasına gidin.

Direktifle, `@page "{id:int}"` kırılma noktası asla vurulmaz. Yönlendirme motoru HTTP 404 döndürür. Kullanarak, `@page "{id:int?}"` `OnGetAsync` yöntem `NotFound` döndürür (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Eşzamanlılık özel durum işlemeyi gözden geçirme

`OnPostAsync` *Sayfalar/Filmler/Edit.cshtml.cs* dosyasındaki yöntemi gözden geçirin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Önceki kod, bir istemci filmi sildiğinde ve diğer istemci filmdeki değişiklikleri yayınladığında eşzamanlılık özel durumlarını algılar.

`catch` Bloğu test etmek için:

* Kesme noktasını ayarlama`catch (DbUpdateConcurrencyException)`
* Film için **Edit'i** seçin, değişiklik yapın, ancak **Kaydet'i**girmeyin.
* Başka bir tarayıcı penceresinde, aynı film için **Sil** bağlantısını seçin ve ardından filmi silin.
* Önceki tarayıcı penceresinde, filmdeki değişiklikleri yayınlayın.

Üretim kodu eşzamanlılık çakışmalarını algılamak isteyebilir. Bkz. Daha fazla bilgi için [eşzamanlılık çakışmalarını ele alın.](xref:data/ef-rp/concurrency)

### <a name="posting-and-binding-review"></a>Gönderme ve bağlayıcı inceleme

*Sayfalar/Filmler/Edit.cshtml.cs* dosyasını inceleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Filmler/Edit sayfasına http GET isteği yapıldığında (örneğin,): `http://localhost:5000/Movies/Edit/2`

* Yöntem, `OnGetAsync` filmi veritabanından getirir ve `Page` yöntemi döndürür. 
* Yöntem, `Page` *Sayfaları/Filmleri/Edit.cshtml* Jilet Sayfasını işler. *Sayfalar/Filmler/Edit.cshtml* dosyası, film modelini sayfada kullanılabilir kılan model yönergesini (`@model RazorPagesMovie.Pages.Movies.EditModel`), içerir.
* Edit formu filmdeki değerlerle birlikte görüntülenir.

Filmler/Edit sayfası yayınlandığında:

* Sayfadaki form değerleri `Movie` özelliğe bağlıdır. Öznitelik `[BindProperty]` Model [bağlama](xref:mvc/models/model-binding)sağlar.

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Model durumunda hatalar varsa (örneğin, `ReleaseDate` bir tarihe dönüştürülemez), form gönderilen değerlerle birlikte görüntülenir.
* Model hatası yoksa, film kaydedilir.

Index, Create ve Delete Razor sayfalarındaki HTTP GET yöntemleri de benzer bir örüntü izler. Jilet `OnPostAsync` Oluştur Sayfasındaki HTTP POST yöntemi, `OnPostAsync` Razor Sayfasını Düzenle'deki yönteme benzer bir örüntü izler.

Arama sonraki öğreticieklenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Önceki: Veritabanıyla](xref:tutorials/razor-pages/sql)
> Çalışma[Sonraki: Arama ekleme](xref:tutorials/razor-pages/search)

::: moniker-end
