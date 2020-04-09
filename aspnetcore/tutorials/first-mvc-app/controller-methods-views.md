---
title: ASP.NET Core'da denetleyici yöntemleri ve görünümleri
author: rick-anderson
description: ASP.NET Core'da denetleyici yöntemleri, görünümleri ve DataAnnotations ile nasıl çalışacağınızı öğrenin.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 87b3cb2f4429157123d30274d1f12cd589c1cc99
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242516"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>ASP.NET Core'da denetleyici yöntemleri ve görünümleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Biz film uygulaması için iyi bir başlangıç var, ama sunum ideal değil, örneğin, **ReleaseDate** iki kelime olmalıdır.

![Dizin görünümü: Çıkış Tarihi tek bir kelimedir (boşluk yok) ve her film çıkış tarihi 12 AM'lik bir saati gösterir](working-with-sql/_static/m55.png)

*Modeller/Movie.cs* dosyasını açın ve aşağıda gösterilen vurgulanan satırları ekleyin:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Bir sonraki öğreticide [DataAnnotations'ı](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) ele alıyoruz. [Ekran](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği, bir alanın adı için ne görüntüleneceklerini belirtir (bu durumda "Yayın Tarihi" yerine "Yayın Tarihi" ). [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği, veri türünü (Tarih) belirtir, böylece alanda depolanan saat bilgileri görüntülenmez.

Entity `[Column(TypeName = "decimal(18, 2)")]` Framework Core'un veritabanındaki para birimiyle `Price` doğru şekilde eşlenebilmeleri için veri ek açıklamagereklidir. Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)

Denetleyiciye `Movies` göz atın ve hedef URL'yi görmek için fare işaretçisini bir **Edit** bağlantısıüzerinde tutun.

![Edit bağlantısı üzerinde fare ile tarayıcı penceresi https://localhost:5001/Movies/Edit/5 ve bir bağlantı Url gösterilir](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

**Düzenleme,** **Ayrıntılar**ve **Silme** *bağlantıları, Görünümler/Filmler/Index.cshtml* dosyasındaki Core MVC Bağlantı Etiketi Yardımcısı tarafından oluşturulur.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir. Yukarıdaki kodda, `AnchorTagHelper` dinamik olarak denetleyici `href` eylem yöntemi ve rota id HTML öznitelik değeri oluşturur. Sık kullandığınız tarayıcıdan **Kaynak Görünümü'nu** veya oluşturulan biçimlendirmeyi incelemek için geliştirici araçlarını kullanırsınız. Oluşturulan HTML'nin bir bölümü aşağıda gösterilmiştir:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

*Startup.cs* dosyasında ayarlanan [yönlendirme](xref:mvc/controllers/routing) biçimini geri çağırın:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core, `https://localhost:5001/Movies/Edit/4` 4 `Edit` `Movies` parametresi `Id` ile denetleyicinin eylem yöntemine bir istek çevirir. (Denetleyici yöntemleri eylem yöntemleri olarak da bilinir.)

[Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) ASP.NET Core en popüler yeni özelliklerinden biridir. Daha fazla bilgi için [ek kaynaklara](#additional-resources)bakın.

<a name="get-post"></a>

Denetleyiciyi `Movies` açın ve `Edit` iki eylem yöntemini inceleyin. Aşağıdaki kod, `HTTP GET Edit` filmi getiren ve *Edit.cshtml* Razor dosyası tarafından oluşturulan düzenleme formunu dolduran yöntemi gösterir.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Aşağıdaki kod, `HTTP POST Edit` deftere nakledilen film değerlerini işleyen yöntemi gösterir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Aşağıdaki kod, `HTTP POST Edit` deftere nakledilen film değerlerini işleyen yöntemi gösterir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[Bind]` [Öznitelik, aşırı deftere nakledene](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)karşı korumanın bir yoludur. Yalnızca değiştirmek istediğiniz özniteliğe özellikler `[Bind]` eklemeniz gerekir. Daha fazla bilgi için [bkz.](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) [ViewModels,](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) aşırı deftere nakil olmasını önlemek için alternatif bir yaklaşım sağlar.

İkinci `Edit` eylem yönteminin öznitelikten `[HttpPost]` önce olduğuna dikkat edin.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

Öznitelik, `HttpPost` bu `Edit` yöntemin *yalnızca* istekler için `POST` çağrılabileceğini belirtir. Özniteliği ilk `[HttpGet]` edit yöntemine uygulayabilirsiniz, ancak varsayılan `[HttpGet]` olduğu için bu gerekli değildir.

Öznitelik, `ValidateAntiForgeryToken` bir [isteğin sahtesini önlemek](xref:security/anti-request-forgery) için kullanılır ve düzenlenen görünüm dosyasında oluşturulan sahteciliğe karşı bir belirteçle eşlenir *(Görünümler/Filmler/Edit.cshtml).* Görünüm edin, [Form Tag Helper](xref:mvc/views/working-with-forms)ile sahteciliği önleme belirteci oluşturur.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Form Tag Helper,](xref:mvc/views/working-with-forms) Filmler denetleyicisi `[ValidateAntiForgeryToken]` `Edit` yönteminde oluşturulan sahtecilik önleme belirteciyle eşleşmesi gereken gizli bir sahtecilik önleme belirteci oluşturur. Daha fazla bilgi için [Anti-İstek Sahteciliği'ne](xref:security/anti-request-forgery)bakın.

Yöntem `HttpGet Edit` film `ID` parametresini alır, Varlık Çerçevesi `FindAsync` yöntemini kullanarak filmi arar ve seçili filmi Edit görünümüne döndürür. Bir film bulunamıyorsa, `NotFound` (HTTP 404) döndürülür.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

İskele sistemi Düzenle görünümünü oluşturduğunda, `Movie` sınıfı incelemiş ve sınıfın `<label>` `<input>` her özelliği için işlemek için kod ve öğeler oluşturmıştır. Aşağıdaki örnek, Visual Studio iskele sistemi tarafından oluşturulan Düzenle görünümünü gösterir:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Görünüm şablonunun dosyanın `@model MvcMovie.Models.Movie` üst kısmında nasıl bir deyim ekibe sahip olduğunu öğrenin. `@model MvcMovie.Models.Movie`görünümün görünüm şablonu modelinin türde `Movie`olmasını beklediğini belirtir.

İskele kodu, HTML biçimlendirmesini kolaylaştırmak için birkaç Etiket Yardımcısı yöntemi kullanır. The - [Label Tag Helper](xref:mvc/views/working-with-forms) alanın adını görüntüler ("Başlık", "Yayın Tarihi", "Tür", veya "Fiyat"). [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms) bir HTML `<input>` öğesi işler. [Doğrulama Etiketi Yardımcısı,](xref:mvc/views/working-with-forms) bu özellik ile ilişkili doğrulama iletilerini görüntüler.

Uygulamayı çalıştırın ve `/Movies` URL'ye gidin. Bir **Edit** bağlantısını tıklatın. Tarayıcıda, sayfanın kaynağını görüntüleyin. `<form>` Öğe için oluşturulan HTML aşağıda gösterilmiştir.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Öğeler, `<input>` özniteliği `HTML <form>` URL'ye nakletmek üzere ayarlanmış bir öğededir. `action` `/Movies/Edit/id` `Save` Düğme tıklatıldığında form verileri sunucuya nakledilir. Kapanış `</form>` öğesinden önceki son satır, [Form Tag Helper](xref:mvc/views/working-with-forms)tarafından oluşturulan gizli [XSRF](xref:security/anti-request-forgery) belirteci gösterir.

## <a name="processing-the-post-request"></a>POSTA İsteğinin İşlenmesi

Aşağıdaki liste, `[HttpPost]` eylem yönteminin `Edit` sürümünü gösterir.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]` [Öznitelik, Form Tag Helper'Daki](xref:mvc/views/working-with-forms) sahteciliğe karşı verme üreteci tarafından oluşturulan gizli [XSRF](xref:security/anti-request-forgery) belirtecidoğrular

[Model bağlama](xref:mvc/models/model-binding) sistemi deftere nakledilen form `Movie` değerlerini alır ve `movie` parametre olarak geçirilen bir nesne oluşturur. Yöntem, `ModelState.IsValid` formda gönderilen verilerin bir `Movie` nesneyi değiştirmek (değiştirmek veya güncelleştirmek) için kullanılabileceğini doğrular. Veriler geçerliyse, kaydedilir. Güncelleştirilmiş (düzenlenmiş) film verileri veritabanı bağlamı `SaveChangesAsync` yöntemi çağırılarak veritabanına kaydedilir. Verileri kaydettikten sonra kod, kullanıcıyı, `Index` yeni yapılan `MoviesController` değişiklikler de dahil olmak üzere film koleksiyonunu görüntüleyen sınıfın eylem yöntemine yönlendirir.

Form sunucuya gönderilmeden önce, istemci tarafı doğrulama alanları üzerindeki doğrulama kurallarını denetler. Herhangi bir doğrulama hatası varsa, bir hata iletisi görüntülenir ve form deftere nakledilmemiştir. JavaScript devre dışı bırakılırsa, istemci tarafı doğrulamanız olmaz, ancak sunucu deftere nakledilen değerleri algılar ve form değerleri hata iletileriyle yeniden görüntülenir. Daha sonra öğretici de daha ayrıntılı olarak [Model Doğrulama](xref:mvc/models/validation) inceleyin. *Görünümler/Filmler/Edit.cshtml* görünüm şablonundaki [Doğrulama Etiketi Yardımcısı,](xref:mvc/views/working-with-forms) uygun hata iletilerini görüntülemeyi sağlar.

![Görünüm edin: ABC'nin hatalı Fiyat değeri için bir istisna, alan Fiyatı'nın bir sayı olması gerektiğini belirtir. XYZ durumlarının yanlış Çıkış Tarihi değeri için bir istisna Lütfen geçerli bir tarih girin.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Film `HttpGet` denetleyicisindeki tüm yöntemler benzer bir desen izler. Bir film nesnesi (veya nesnelerin listesini, `Index`durumda) almak ve görünüme nesne (model) geçmek. Yöntem `Create` boş bir film nesnesini `Create` görünüme geçirir. Verileri oluşturan, düzenleyen, silen veya başka bir `[HttpPost]` şekilde değiştiren tüm yöntemler, yöntemin aşırı yüklenmesinde bunu yapar. Bir `HTTP GET` yöntemde verileri değiştirmek bir güvenlik riskidir. Bir `HTTP GET` yöntemdeki verileri değiştirmek, HTTP en iyi uygulamalarını ve GET isteklerinin uygulamanızın durumunu değiştirmemesi gerektiğini belirten mimari [REST](http://rest.elkstein.org/) deseni de ihlal eder. Başka bir deyişle, GET işlemini gerçekleştirmek, yan etkisi olmayan ve kalıcı verilerinizi değiştirmeyen güvenli bir işlem olmalıdır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)
* [Tag Helpers Giriş](xref:mvc/views/tag-helpers/intro)
* [Yazar Etiketi Yardımcıları](xref:mvc/views/tag-helpers/authoring)
* [Anti-İstek Sahteciliği](xref:security/anti-request-forgery)
* Kumandanızı [aşırı deftere nakilden](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) koruyun
* [Modelleri Görüntüle](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Form Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Etiket Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Seçim Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Doğrulama Etiketi Yardımcısı](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Önceki](working-with-sql.md)
> [Sonraki](search.md)  
