---
title: ASP.NET Core Bölüm 6, denetleyici yöntemleri ve görünümleri
author: rick-anderson
description: 6. bölüm, ASP.NET Core MVC uygulamasına model ekleme
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050777"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>ASP.NET Core Bölüm 6, denetleyici yöntemleri ve görünümleri

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Film uygulamasına iyi başladık, ancak sunu ideal değildir, örneğin, **ReleaseDate** iki sözcükten oluşmalıdır.

![Dizin görünümü: Yayın tarihi bir sözcükdür (boşluk yok) ve her film yayın tarihi 12 saat içinde görüntülenir](working-with-sql/_static/m55.png)

*Modeller/film. cs* dosyasını açın ve vurgulanan satırları aşağıda gösterildiği gibi ekleyin:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Sonraki öğreticide [veri ek açıklamalarını](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) ele aldık. [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) özniteliği bir alanın adı için (Bu durumda "ReleaseDate" yerine "Yayın tarihi") görüntüleneceğini belirtir. [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) özniteliği verilerin türünü belirtir (Tarih), bu nedenle alanda depolanan zaman bilgileri gösterilmez.

`[Column(TypeName = "decimal(18, 2)")]`Entity Framework Core veri ek açıklaması gerekir, bu nedenle `Price` veritabanında para birimiyle doğru şekilde eşleşebilirler. Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

`Movies`Denetleyiciyi inceleyin ve hedef URL 'yi görmek için fare işaretçisini bir **düzenleme** bağlantısı üzerine tutun.

![Düzenleme bağlantısı üzerinde fare ile tarayıcı penceresi ve bağlantı URL 'Si https://localhost:5001/Movies/Edit/5 gösteriliyor](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

**Düzenle**, **Ayrıntılar** ve **Sil** bağlantıları, *Görünümler/fılmler/Index. cshtml* dosyasındaki Core MVC bağlayıcı etiketi Yardımcısı tarafından oluşturulur.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor . Yukarıdaki kodda, `AnchorTagHelper` `href` denetleyici eylem yönteminden ve yol kimliğinden HTML özniteliği değeri dinamik olarak oluşturulur. En sevdiğiniz tarayıcıdan **Görünüm kaynağını** kullanıyorsunuz veya oluşturulan biçimlendirmeyi incelemek için geliştirici araçlarını kullanıyorsunuz. Oluşturulan HTML 'nin bir bölümü aşağıda gösterilmiştir:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

*Startup.cs* dosyasındaki [yönlendirme](xref:mvc/controllers/routing) kümesi için biçimi geri çekin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core, `https://localhost:5001/Movies/Edit/4` `Edit` denetleyicinin Action yöntemine `Movies` 4 parametresi ile dönüştürür `Id` . (Denetleyici yöntemleri de eylem yöntemleri olarak bilinir.)

[Etiket yardımcıları](xref:mvc/views/tag-helpers/intro) ASP.NET Core ' deki en popüler yeni özelliklerden biridir. Daha fazla bilgi için bkz. [ek kaynaklar](#additional-resources).

<a name="get-post"></a>

Denetleyiciyi açın `Movies` ve iki `Edit` eylem yöntemini inceleyin. Aşağıdaki kod, `HTTP GET Edit` filmi getiren ve *Edit. cshtml* dosyası tarafından oluşturulan düzenleme formunu dolduran yöntemini gösterir Razor .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Aşağıdaki kod, `HTTP POST Edit` postalanan film değerlerini işleyen yöntemini gösterir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Aşağıdaki kod, `HTTP POST Edit` postalanan film değerlerini işleyen yöntemini gösterir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Özniteliği, daha `[Bind]` [fazla](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost)gönderim için bir yoldur. Yalnızca değiştirmek istediğiniz özniteliğe özellikler dahil etmelisiniz `[Bind]` . Daha fazla bilgi için, bkz. [denetleyicinizi yeniden gönderme durumundan koruma](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [Viewmodeller](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) , daha fazla nakletmeyi engellemek için alternatif bir yaklaşım sağlar.

İkinci `Edit` eylem yönteminin önünde özniteliği olduğuna dikkat edin `[HttpPost]` .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost`Öznitelik, bu `Edit` yöntemin *yalnızca* istekler için çağrılabilecek olduğunu belirtir `POST` . `[HttpGet]`Özniteliği ilk düzenleme yöntemine uygulayabilirsiniz, ancak bu gerekli değildir çünkü `[HttpGet]` varsayılan.

`ValidateAntiForgeryToken`Özniteliği, [bir isteğin bir kısmını engellemek](xref:security/anti-request-forgery) için kullanılır ve düzenleme görünümü dosyasında (*Görünümler/filmler/Düzenle. cshtml*) oluşturulan bir güvenlik yumuşatma belirteci ile eşleştirilmiş olur. Düzenleme görünümü dosyası, [form etiketi Yardımcısı](xref:mvc/views/working-with-forms)ile karşı koruma belirteci oluşturur.

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Form etiketi Yardımcısı](xref:mvc/views/working-with-forms) , `[ValidateAntiForgeryToken]` film denetleyicisi yönteminde üretilen Anti-forgery belirteciyle eşleşmesi gereken gizli bir Anti-forgery belirteci oluşturur `Edit` . Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>.

`HttpGet Edit`Yöntemi, film parametresini alır `ID` , Entity Framework yöntemini kullanarak filmi arar `FindAsync` ve seçili filmi düzenleme görünümüne döndürür. Bir film bulunamazsa `NotFound` (HTTP 404) döndürülür.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Scafkatlama sistemi düzenleme görünümü oluştururken, sınıfını ve `Movie` `<label>` `<input>` sınıfının her bir özelliği için işlenecek kodu ve öğelerini inceledi. Aşağıdaki örnekte, Visual Studio scafkatlama sistemi tarafından oluşturulan düzenleme görünümü gösterilmektedir:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Görünüm şablonunun dosyanın en üstünde bir ifadeye sahip olduğuna dikkat edin `@model MvcMovie.Models.Movie` . `@model MvcMovie.Models.Movie` görünümün görünüm şablonu için modelin türü olmasını beklediğini belirtir `Movie` .

Scafkatlanmış kod, HTML işaretlemesini kolaylaştırmak için birkaç etiket Yardımcısı yöntemi kullanır. [Etiket etiketi Yardımcısı](xref:mvc/views/working-with-forms) alanın adını ("title", "ReleaseDate", "tarz" veya "Price") görüntüler. [Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) bir HTML öğesi işler `<input>` . [Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms) , bu özellikle ilişkili tüm doğrulama iletilerini görüntüler.

Uygulamayı çalıştırın ve URL 'ye gidin `/Movies` . Bir **düzenleme** bağlantısına tıklayın. Tarayıcıda, sayfanın kaynağını görüntüleyin. Öğesi için oluşturulan HTML `<form>` aşağıda gösterilmiştir.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>`Öğeleri `HTML <form>` `action` özniteliği URL 'ye gönderi olarak ayarlanan bir öğedir `/Movies/Edit/id` . Düğme tıklandığında form verileri sunucuya gönderilir `Save` . Kapanış öğesinden önceki son satır, `</form>` [form etiketi Yardımcısı](xref:mvc/views/working-with-forms)tarafından oluşturulan gizli [XSRF](xref:security/anti-request-forgery) belirtecini gösterir.

## <a name="processing-the-post-request"></a>POST Isteği işleniyor

Aşağıdaki listede `[HttpPost]` `Edit` eylem yönteminin sürümü gösterilmektedir.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]`Öznitelik, [form etiketi Yardımcısı](xref:mvc/views/working-with-forms) 'nda Anti-forgery belirteç Oluşturucu tarafından oluşturulan gizli [XSRF](xref:security/anti-request-forgery) belirtecini doğrular

[Model bağlama](xref:mvc/models/model-binding) sistemi, postalanan form değerlerini alır ve `Movie` parametresi olarak geçirilmiş bir nesne oluşturur `movie` . `ModelState.IsValid`Özelliği, formda gönderilen verilerin bir nesneyi değiştirmek (düzenlemek veya güncelleştirmek) için kullanılabileceğini doğrular `Movie` . Veriler geçerliyse, kaydedilir. Güncelleştirilmiş (düzenlenmiş) film verileri veritabanı bağlamı yöntemine çağrı yaparak veritabanına kaydedilir `SaveChangesAsync` . Veriler kaydedildikten sonra kod, kullanıcıyı, `Index` `MoviesController` Yeni yapılan değişiklikler dahil olmak üzere, film koleksiyonunu görüntüleyen sınıfının Action yöntemine yönlendirir.

Form sunucuya gönderilmeden önce, istemci tarafı doğrulaması alanlarda tüm doğrulama kurallarını denetler. Herhangi bir doğrulama hatası varsa, bir hata iletisi görüntülenir ve form nakledilmez. JavaScript devre dışıysa, istemci tarafı doğrulamaya sahip olmayacaktır, ancak sunucu geçerli olmayan gönderilen değerleri tespit eder ve form değerleri hata iletileriyle birlikte görüntülenir. Öğreticide daha sonra [model doğrulamayı](xref:mvc/models/validation) daha ayrıntılı bir şekilde inceleyeceğiz. *Görünümler/filmler/Edit. cshtml* görünüm şablonundaki [doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms) , uygun hata iletilerini görüntülemeyi üstlenir.

![Görünümü Düzenle: ABC durumlarının yanlış bir fiyat değeri için bir özel durum, alan fiyatının bir sayı olması gerektiğini belirtir. Xyz durumlarının yanlış Yayın tarihi değeri için bir özel durum lütfen geçerli bir tarih girin.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

`HttpGet`Film denetleyicisindeki tüm yöntemler benzer bir düzene uyar. Bir film nesnesi (veya bunun durumunda nesne listesi `Index` ) alır ve nesneyi (model) görünüme iletir. `Create`Yöntemi, görünüme boş bir film nesnesi geçirir `Create` . Verileri oluşturan, düzenleme, silme veya başka bir şekilde değiştiren tüm yöntemler `[HttpPost]` , yönteminin aşırı yüküne neden olacak. Bir yöntemde verileri değiştirme `HTTP GET` bir güvenlik riskidir. Bir yöntemde verileri değiştirmek `HTTP GET` , http en iyi uygulamalarını ve bu da GET isteklerinin uygulamanızın durumunu değiştirmemesi gerektiğini belirten mimari [rest](http://rest.elkstein.org/) modelini ihlal ediyor. Diğer bir deyişle, bir GET işleminin gerçekleştirilmesi, yan etkileri olmayan ve kalıcı verilerinizi değiştirmeyen bir güvenli işlem olmalıdır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)
* [Etiket yardımcılarına giriş](xref:mvc/views/tag-helpers/intro)
* [Yazar etiketi yardımcıları](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* Denetleyicinizi [yeniden gönderme](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) durumundan koruma
* [ViewModel 'lar](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Form Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Etiket Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Seçim Etiketi Yardımcısı](xref:mvc/views/working-with-forms)
* [Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Önceki](working-with-sql.md) 
>  [Sonraki](search.md)  
