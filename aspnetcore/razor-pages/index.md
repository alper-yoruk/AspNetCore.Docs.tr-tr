---
title: ASP.NET Core Razor sayfalara giriş
author: Rick-Anderson
description: ASP.NET Core sayfalarındaki Razor SAYFALARıN, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 827a0df4c914fd012c55e6612a987713bfbaa5c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777221"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>ASP.NET Core Razor Pages giriş

::: moniker range=">= aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)

Razor Pages, kodlama sayfasına odaklanmış senaryolar denetleyicileri ve görünümleri kullanmaktan daha kolay ve daha üretken hale getirebilirsiniz.

Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).

Bu belge Razor Pages bir giriş sağlar. Adım adım öğretici değildir. Bölümlerden bazılarını çok gelişmiş bir şekilde bulursanız, bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start). ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Razor Pages projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut `dotnet new webapp` satırından çalıştırın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut `dotnet new webapp` satırından çalıştırın.

Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.

---

## <a name="razor-pages"></a>Razor Pages

Razor Pages, *Startup.cs*'de etkinleştirilmiştir:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi bir çok şey arar. Bu, [`@page`](xref:mvc/views/razor#page) farklı kılan yönergedir. `@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`sayfada ilk Razor yönergesi olmalıdır. `@page`diğer [Razor](xref:mvc/views/razor) yapıları davranışını etkiler. Razor Pages dosya adlarında *. cshtml* soneki vardır.

Bir `PageModel` sınıf kullanan benzer bir sayfa aşağıdaki iki dosyada gösterilmiştir. *Pages/Index2. cshtml* dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2. cshtml. cs* sayfa modeli:

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Kurala göre, `PageModel` sınıf dosyası *. cs* eklenmiş Razor sayfası dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor sayfası *Pages/Index2. cshtml*' dir. `PageModel` Sınıfını Içeren dosya *sayfa/Index2. cshtml. cs*olarak adlandırılır.

URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yolu               | eşleşen URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` veya `/Index` |
| */Pages/Contact.exe* | `/Contact` |
| */Pages/Store/Contact.exe* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı, *Sayfalar* klasöründeki Razor Pages dosyaları varsayılan olarak arar.
* `Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.

## <a name="write-a-basic-form"></a>Temel form yazma

Razor Pages, Web tarayıcıları ile kullanılan ortak desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır. [Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi, Razor sayfası sınıfında tanımlanan özelliklerle *çalışır* . `Contact` Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün:

Bu belgedeki `DbContext` örnekler için, [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Veri modeli:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

DB bağlamı:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

*Pages/Create. cshtml. cs* sayfa modeli:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.

`PageModel` Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar. Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar. Bu ayrım şunları sağlar:

* [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.
* [Birim testi](xref:test/razor-pages-tests)

Sayfada, istekler üzerinde `OnPostAsync` `POST` çalışan bir *işleyici yöntemi*vardır (bir Kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici metotları eklenebilir. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* Razor sayfasını görüntüler.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Yukarıdaki kod Razor Pages için tipik bir davranıştır.

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:

* Yukarıdaki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.
* [Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve Razor Pages aynı şekilde çalışır. 

Önceki `OnPostAsync` Yöntem:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel akışı `OnPostAsync`:

Doğrulama hatalarını kontrol edin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Sayfalardan işlenmiş HTML */Create. cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Önceki kodda, formu deftere nakletme:

* Geçerli verilerle:

  * `OnPostAsync` Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır. `RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür. `RedirectToPage`:

    * Bir eylem sonucudur.
    * , Veya `RedirectToRoute` ile `RedirectToAction` benzerdir (denetleyiciler ve görünümlerde kullanılır).
    * Sayfalar için özelleştirilir. Yukarıdaki örnekte, kök dizin sayfasına (`/Index`) yeniden yönlendiriliyor. `RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

* Sunucuya geçirilen doğrulama hatalarıyla birlikte:

  * `OnPostAsync` Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemini çağırır. `Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür. Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine `View`benzer. `PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür. Döndüren `void` bir işleyici yöntemi sayfayı işler.
  * Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor. Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler. Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:

  * Veriler sunucuya **nakledilmedi.**
  * İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.

Özelliği `Customer` , model [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) bağlamasını kabul etmek için özniteliğini kullanır:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.** Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).

Razor Pages, varsayılan olarak yalnızca`GET` fiiller olmayan özellikleri bağlayın. Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır. Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır (`<input asp-for="Customer.Name">`) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Etiket Yardımcıları kullanılabilir hale getirir.

### <a name="the-home-page"></a>Giriş sayfası

*Index. cshtml* giriş sayfasıdır:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

İlişkili `PageModel` sınıf (*Index.cshtml.cs*):

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , düzenleme sayfasına `asp-route-{value}` bir bağlantı oluşturmak için özniteliğini kullandı. Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.

*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

İşlenmiş HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:

* `asp-route-id` Özniteliği tarafından belirtilen MÜŞTERI iletişim kimliği.
* `handler`, `asp-page-handler` Özniteliği tarafından belirtilen.

Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir. Kurala göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.

Bu örnekte olduğundan `OnPostDeleteAsync` , `POST` isteği işlemek için işleyici yöntemi kullanılır. `handler` `delete` `asp-page-handler` , Gibi farklı bir değere `remove`ayarlandıysa, adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

`OnPostDeleteAsync` Yöntemi:

* `id` Sorgu dizesinden alır.
* Müşteri iletişim için veritabanını sorgular `FindAsync`.
* Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.
* Kök <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> dizin sayfasına (`/Index`) yeniden yönlendirmek için çağrılar.

### <a name="the-editcshtml-file"></a>Edit. cshtml dosyası

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

İlk satır `@page "{id:int}"` yönergesini içerir. Yönlendirme kısıtlaması`"{id:int}"` , sayfada `int` yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler. Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int`, çalışma zamanı bir HTTP 404 (bulunamadı) hatası döndürür. KIMLIĞI isteğe bağlı yapmak için yol kısıtlamasına `?` ekleyin:

 ```cshtml
@page "{id:int?}"
```

*Edit.cshtml.cs* dosyası:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Doğrulama

Doğrulama kuralları:

* Model sınıfında bildirimli olarak belirtilir.
* Uygulamada her yerde zorlanır.

Ad <xref:System.ComponentModel.DataAnnotations> alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar. Dataaçıklamalarda, biçimlendirme ile ilgili Yardım [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.

`Customer` Modeli göz önünde bulundurun:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Yukarıdaki kod:

* JQuery ve jQuery doğrulama betikleri içerir.
* `<div />` ' İ etkinleştirmek `<span />` için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:

  * İstemci tarafı doğrulama.
  * Doğrulama hatası işleme.

* Aşağıdaki HTML 'yi oluşturur:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler. formunda. İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.

`[StringLength(10)]` ÖZNITELIĞI işlenmiş html `data-val-length-max="10"` üzerinde oluşturulur. `data-val-length-max`tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller. Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:

* , Adı 10 ' dan daha uzun.
* "Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi. döndürülür.

Aşağıdaki `Movie` modeli göz önünde bulundurun:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:

* `Required` Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.
* `RegularExpression` Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır. Yukarıdaki kodda, "tarz":

  * Yalnızca harfler kullanılmalıdır.
  * İlk harfin büyük harfle olması gerekir. Boşluk, sayı ve özel karakterlere izin verilmez.

* `RegularExpression` "Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki boşlukların içindeki özel karakter ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* `StringLength` Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.
* Değer türleri (örneğin, `decimal`, `int`, `float` `DateTime`), doğal olarak gereklidir ve `[Required]` özniteliğe gerek kalmaz.

`Movie` Model için Oluştur sayfasında, geçersiz değerlere sahip hatalar görüntülenir:

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

Daha fazla bilgi için bkz.

* [Film uygulamasına doğrulama ekleme](xref:tutorials/razor-pages/validation)
* [ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri

`HEAD`istekler belirli bir kaynak için üstbilgileri almaya izin verir. İsteklerin `GET` aksine `HEAD` istekler bir yanıt gövdesi döndürmez.

Normalde, istekler `OnHead` için `HEAD` bir işleyici oluşturulur ve çağırılır:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages, işleyici tanımlanmadığında `OnGet` `OnHead` işleyiciyi çağırmaya geri döner.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF ve Razor Pages

Razor Pages, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur. [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Razor Pages ile düzenleri, partileri, şablonları ve etiket yardımcılarını kullanma

Sayfalar, Razor görünüm altyapısının tüm özellikleri ile çalışır. Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*ve *_ViewImports. cshtml* geleneksel Razor görünümlerinde oldukları gibi çalışır.

Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.

*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).
* JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.
* Razor sayfasının içerikleri, çağrıldığında işlenir `@RenderBody()` .

Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).

[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/paylaşılan* klasöründedir. Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar. *Sayfalar/paylaşılan* klasördeki bir düzen, *Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.

Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.

Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** . *Görünümler/paylaşılan* bir MVC görünümleri modelidir. Razor Pages, yol kurallarını değil klasör hiyerarşisine güvenmektir.

Bir Razor sayfasından arama görüntüleme, *Sayfalar* klasörünü içerir. MVC denetleyicileri ve geleneksel Razor görünümleriyle kullanılan düzenler, şablonlar ve partilar *yalnızca çalışır*.

Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır. `@addTagHelper` Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.

<a name="namespace"></a>

Bir `@namespace` sayfada ayarlanan yönerge:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

`@namespace` Yönergesi sayfanın ad alanını ayarlar. `@model` Yönergesinin ad alanını içermesi gerekmez.

`@namespace` Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, `@namespace` yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar. Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.

Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Pages/Customers/Edit. cshtml* Razor sayfasının oluşturulan ad alanı `PageModel` sınıfıyla aynıdır.

`@namespace`*Ayrıca geleneksel Razor görünümleriyle birlikte da geçerlidir.*

*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı. Düzen dosyası JavaScript dosyalarını içeri aktardı.

[Razor Pages Starter projesi](#rpvs17) , istemci tarafı doğrulamayı bağlayan *sayfaları/_ValidationScriptsPartial. cshtml*'yi içerir.

Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial>..

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

Daha `Create` önce gösterilen sayfa şunları kullanır `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Pages*

  * *Index.cshtml*
  * *Gizlilik. cshtml*
  * */Customers*

    * *. Cshtml oluştur*
    * *Edit. cshtml*
    * *Index.cshtml*

*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir. Dize `./Index` , önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır. *Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır. Örneğin:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Mutlak sayfa adı `/Index` , *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır. Örneğin:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin, `/Index`) bir yoldur. Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, *sayfalarda/müşteriler/Create. cshtml*'de `RedirectToPage` farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.

| RedirectToPage (x)| Sayfa |
| ----------------- | ------------ |
| RedirectToPage ("/Index") | *Sayfa/dizin* |
| RedirectToPage ("./Index"); | *Sayfalar/müşteriler/Dizin* |
| RedirectToPage (".. /İndex ") | *Sayfa/dizin* |
| RedirectToPage ("Dizin")  | *Sayfalar/müşteriler/Dizin* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` , *göreli adlardır*. `RedirectToPage` Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .

Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur. Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:

* Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.
* Klasör adını içermediği için bağlantılar kopuk değildir.

Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veri, ile <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>bir sayfaya geçirilebilir. `[ViewData]` Özniteliği olan özellikler, <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir.

Aşağıdaki örnekte, `AboutModel` `[ViewData]` özniteliği `Title` özelliğine uygular:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:

```cshtml
<h1>@Model.Title</h1>
```

Mizanpajda, başlık ViewData sözlüğünden okundu:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core, <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>öğesini kullanıma sunar. Bu özellik, okunana kadar verileri depolar. <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir. `TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod, şunu `Message` kullanarak `TempData`değerini ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, `Message` using `TempData`değerini gösterir.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` `Message` özelliğine özniteliğini uygular.

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, `asp-page-handler` etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Yukarıdaki örnekteki formda, her biri farklı bir URL 'ye göndermek `FormActionTagHelper` için kullanan iki gönderme düğmesi vardır. `asp-page-handler` Özniteliği, için `asp-page`bir yardımcı ' dir. `asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.

Sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde `Async` (varsa) ad içindeki metin alınarak oluşturulur. Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir. *Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve `JoinList` `JoinListUC`' dir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, ' a gönderen `OnPostJoinListAsync` URL yolu. `https://localhost:5001/Customers/CreateFATH?handler=JoinList` ' A gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Özel yollar

İçin `@page` yönergesini kullanın:

* Sayfaya özel bir yol belirtin. Örneğin, hakkında sayfasına olan yol ile `/Some/Other/Path` `@page "/Some/Other/Path"`öğesine ayarlanabilir.
* Kesimleri bir sayfanın varsayılan yoluna ekleyin. Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"`.
* Bir sayfanın varsayılan yoluna parametreleri ekleyin. Örneğin, bir ID parametresi `id`, içeren `@page "{id}"`bir sayfa için gerekli olabilir.

Yolun başındaki bir tilde (`~`) tarafından belirlenen kök göreli bir yol desteklenir. Örneğin, `@page "~/Some/Other/Path"` ile `@page "/Some/Other/Path"`aynıdır.

URL 'de sorgu dizesini `?handler=JoinList` beğenmezseniz, URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin. Yol, `@page` yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, ' a gönderen `OnPostJoinListAsync` URL yolu. `https://localhost:5001/Customers/CreateFATH/JoinList` ' A gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.

## <a name="advanced-configuration-and-settings"></a>Gelişmiş yapılandırma ve ayarlar

Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.

Gelişmiş seçenekleri yapılandırmak için genişletme yöntemini <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>kullanın:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Sayfalar için <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin. Kurallar hakkında daha fazla bilgi için bkz. [Razor Pages yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).

Görünümleri önceden derlemek için bkz. [Razor görünüm derlemesi](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Razor Pages içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor Pages */Pages* dizininde kök olarak depolanır. Razor Pages <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> , uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) olduğunu belirtmek için ekleyin:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Razor Pages özel kök dizinde olduğunu belirtin

Razor Pages <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> , uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* Bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start), bu giriş hakkında derleme
* [Örnek kodu indirme veya görüntüleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)

Razor Pages, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir yönüdür.

Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).

Bu belge Razor Pages bir giriş sağlar. Adım adım öğretici değildir. Bölümlerden bazılarını çok gelişmiş bir şekilde bulursanız, bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start). ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Razor Pages projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [Razor Pages kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start) .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut `dotnet new webapp` satırından çalıştırın.

Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut `dotnet new webapp` satırından çalıştırın.

---

## <a name="razor-pages"></a>Razor Pages

Razor Pages, *Startup.cs*'de etkinleştirilmiştir:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi bir çok şey arar. Bu, `@page` farklı kılan yönergedir. `@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`sayfada ilk Razor yönergesi olmalıdır. `@page`diğer Razor yapıları davranışını etkiler.

Bir `PageModel` sınıf kullanan benzer bir sayfa aşağıdaki iki dosyada gösterilmiştir. *Pages/Index2. cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2. cshtml. cs* sayfa modeli:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Kurala göre, `PageModel` sınıf dosyası *. cs* eklenmiş Razor sayfası dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor sayfası *Pages/Index2. cshtml*' dir. `PageModel` Sınıfını Içeren dosya *sayfa/Index2. cshtml. cs*olarak adlandırılır.

URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yolu               | eşleşen URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` veya `/Index` |
| */Pages/Contact.exe* | `/Contact` |
| */Pages/Store/Contact.exe* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı, *Sayfalar* klasöründeki Razor Pages dosyaları varsayılan olarak arar.
* `Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.

## <a name="write-a-basic-form"></a>Temel form yazma

Razor Pages, Web tarayıcıları ile kullanılan ortak desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır. [Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi, Razor sayfası sınıfında tanımlanan özelliklerle *çalışır* . `Contact` Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün:

Bu belgedeki `DbContext` örnekler için, [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Veri modeli:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

DB bağlamı:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

*Pages/Create. cshtml. cs* sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.

`PageModel` Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar. Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar. Bu ayrım şunları sağlar:

* [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.
* Sayfaların [birim testi](xref:test/razor-pages-tests) .

Sayfada, istekler üzerinde `OnPostAsync` `POST` çalışan bir *işleyici yöntemi*vardır (bir Kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. [OnGet](#OnGet) örneği.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Yukarıdaki kod Razor Pages için tipik bir davranıştır.

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:

* Yukarıdaki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.
* [Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.

Önceki `OnPostAsync` Yöntem:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel akışı `OnPostAsync`:

Doğrulama hatalarını kontrol edin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin. İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi bir örneğini döndürmek için `RedirectToPage` yardımcı yöntemini çağırır. `RedirectToPageResult` `RedirectToPage`, `RedirectToAction` veya `RedirectToRoute`' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur. Yukarıdaki örnekte, kök dizin sayfasına (`/Index`) yeniden yönlendiriliyor. `RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen),`OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır. `Page`, bir `PageResult` örneği döndürür. Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine `View`benzer. `PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür. Döndüren `void` bir işleyici yöntemi sayfayı işler.

`Customer` Özelliği model bağlamayı `[BindProperty]` kabul etmek için özniteliğini kullanır.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages, varsayılan olarak yalnızca`GET` fiiller olmayan özellikleri bağlayın. Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir. Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır (`<input asp-for="Customer.Name">`) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

Giriş sayfası (*Index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

İlişkili `PageModel` sınıf (*Index.cshtml.cs*):

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , düzenleme sayfasına `asp-route-{value}` bir bağlantı oluşturmak için özniteliğini kullandı. Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir. Etiket Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

*Pages/Edit. cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

İlk satır `@page "{id:int}"` yönergesini içerir. Yönlendirme kısıtlaması`"{id:int}"` , sayfada `int` yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler. Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int`, çalışma zamanı bir HTTP 404 (bulunamadı) hatası döndürür. KIMLIĞI isteğe bağlı yapmak için yol kısıtlamasına `?` ekleyin:

 ```cshtml
@page "{id:int?}"
```

*Pages/Edit. cshtml. cs* dosyası:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Sil düğmesi HTML biçiminde işlendiğinde, için parametreleri `formaction` içerir:

* `asp-route-id` Özniteliği tarafından belirtilen MÜŞTERI iletişim kimliği.
* `asp-page-handler` Özniteliği tarafından `handler` belirtilen.

Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir. Kurala göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.

Bu örnekte olduğundan `OnPostDeleteAsync` , `POST` isteği işlemek için işleyici yöntemi kullanılır. `handler` `delete` `asp-page-handler` , Gibi farklı bir değere `remove`ayarlandıysa, adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir. Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync` Yöntemi:

* `id` Sorgu dizesinden öğesini kabul eder. *Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması `"{id:int?}"`içeriyorsa, `id` rota verilerinden gelir. İçin `id` rota verileri, gibi URI 'de belirtilmiştir `https://localhost:5001/Customers/2`.
* Müşteri iletişim için veritabanını sorgular `FindAsync`.
* Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır. Veritabanı güncelleştirildi.
* Kök `RedirectToPage` dizin sayfasına (`/Index`) yeniden yönlendirmek için çağrılar.

## <a name="mark-page-properties-as-required"></a>Sayfa özelliklerini gerektiği gibi işaretle

`PageModel` Içindeki Özellikler [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri

`HEAD`istekleri belirli bir kaynak için üstbilgileri almanıza izin verir. İsteklerin `GET` aksine `HEAD` istekler bir yanıt gövdesi döndürmez.

Normalde, istekler `OnHead` için `HEAD` bir işleyici oluşturulur ve çağırılır: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

ASP.NET Core 2,1 veya sonraki bir sürümde, hiçbir `OnGet` `OnHead` işleyici tanımlanmazsa, Razor Pages işleyiciyi çağırmaya geri döner. Bu davranış, içindeki `Startup.ConfigureServices` [setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Varsayılan Şablonlar ASP.NET Core 2,1 ve `SetCompatibilityVersion` 2,2 ' de çağrıyı oluşturur. `SetCompatibilityVersion`Razor Pages seçeneğini `AllowMappingHeadRequestsToGetHandler` etkin bir şekilde `true`ayarlar.

İle `SetCompatibilityVersion`tüm davranışlardan çıkmak yerine, açıkça *belirli* davranışları kabul edebilirsiniz. Aşağıdaki kod, isteklerin `HEAD` `OnGet` işleyiciye eşlenmesine izin vermek için ' de kullanılır:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF ve Razor Pages

[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez. Antiforgery belirteci oluşturma ve doğrulama, Razor Pages otomatik olarak eklenir.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Razor Pages ile düzenleri, partileri, şablonları ve etiket yardımcılarını kullanma

Sayfalar, Razor görünüm altyapısının tüm özellikleri ile çalışır. Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*, *_ViewImports. cshtml* geleneksel Razor görünümlerinde oldukları gibi çalışır.

Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.

*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).
* JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.

Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .

[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/paylaşılan* klasöründedir. Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar. *Sayfalar/paylaşılan* klasördeki bir düzen, *Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.

Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.

Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** . *Görünümler/paylaşılan* bir MVC görünümleri modelidir. Razor Pages, yol kurallarını değil klasör hiyerarşisine güvenmektir.

Bir Razor sayfasından arama görüntüleme, *Sayfalar* klasörünü içerir. MVC denetleyicileri ve geleneksel Razor görünümleriyle kullandığınız düzenler, şablonlar ve parals işlemleri *yalnızca çalışır*.

Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır. `@addTagHelper` Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.

<a name="namespace"></a>

`@namespace` Yönerge bir sayfada açıkça kullanıldığında:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Yönergesi sayfanın ad alanını ayarlar. `@model` Yönergesinin ad alanını içermesi gerekmez.

`@namespace` Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, `@namespace` yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar. Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.

Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Pages/Customers/Edit. cshtml* Razor sayfasının oluşturulan ad alanı `PageModel` sınıfıyla aynıdır.

`@namespace`*Ayrıca geleneksel Razor görünümleriyle birlikte da geçerlidir.*

Özgün *Sayfalar/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Razor Pages Starter projesi](#rpvs17) , istemci tarafı doğrulamayı bağlayan *sayfaları/_ValidationScriptsPartial. cshtml*'yi içerir.

Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial>..

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

Daha `Create` önce gösterilen sayfa şunları kullanır `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *. Cshtml oluştur*
    * *Edit. cshtml*
    * *Index.cshtml*

*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir. Dize `/Index` , önceki sayfaya erışmek için URI 'nin bir parçasıdır. Dize `/Index` , *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir. Örneğin:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin, `/Index`) bir yoldur. Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, *sayfa/müşteri/oluşturma. cshtml*'den `RedirectToPage` farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:

| RedirectToPage (x)| Sayfa |
| ----------------- | ------------ |
| RedirectToPage ("/Index") | *Sayfa/dizin* |
| RedirectToPage ("./Index"); | *Sayfalar/müşteriler/Dizin* |
| RedirectToPage (".. /İndex ") | *Sayfa/dizin* |
| RedirectToPage ("Dizin")  | *Sayfalar/müşteriler/Dizin* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` , *göreli adlardır*. `RedirectToPage` Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur. Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz. Tüm bağlantılar hala çalışır (klasör adını içermediği için).

Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir. ' Deki denetleyicilerde Razor veya sayfa modellerinde `[ViewData]` bulunan özelliklerin değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.

Aşağıdaki örnekte, ile `AboutModel` `Title` `[ViewData]`işaretlenmiş bir özelliği içerir. `Title` Özelliği hakkında sayfasının başlığına ayarlanır:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Hakkında sayfasında, `Title` özelliğe model özelliği olarak erişin:

```cshtml
<h1>@Model.Title</h1>
```

Mizanpajda, başlık ViewData sözlüğünden okundu:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar. Bu özellik, okunana kadar verileri depolar. `Keep` Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir. `TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod, şunu `Message` kullanarak `TempData`değerini ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, `Message` using `TempData`değerini gösterir.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` `Message` özelliğine özniteliğini uygular.

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, `asp-page-handler` etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Yukarıdaki örnekteki formda, her biri farklı bir URL 'ye göndermek `FormActionTagHelper` için kullanan iki gönderme düğmesi vardır. `asp-page-handler` Özniteliği, için `asp-page`bir yardımcı ' dir. `asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.

Sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde `Async` (varsa) ad içindeki metin alınarak oluşturulur. Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir. *Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve `JoinList` `JoinListUC`' dir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, ' a gönderen `OnPostJoinListAsync` URL yolu. `https://localhost:5001/Customers/CreateFATH?handler=JoinList` ' A gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Özel yollar

İçin `@page` yönergesini kullanın:

* Sayfaya özel bir yol belirtin. Örneğin, hakkında sayfasına olan yol ile `/Some/Other/Path` `@page "/Some/Other/Path"`öğesine ayarlanabilir.
* Kesimleri bir sayfanın varsayılan yoluna ekleyin. Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"`.
* Bir sayfanın varsayılan yoluna parametreleri ekleyin. Örneğin, bir ID parametresi `id`, içeren `@page "{id}"`bir sayfa için gerekli olabilir.

Yolun başındaki bir tilde (`~`) tarafından belirlenen kök göreli bir yol desteklenir. Örneğin, `@page "~/Some/Other/Path"` ile `@page "/Some/Other/Path"`aynıdır.

URL 'de sorgu dizesini `?handler=JoinList` beğenmezseniz, URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin. Yol, `@page` yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, ' a gönderen `OnPostJoinListAsync` URL yolu. `https://localhost:5001/Customers/CreateFATH/JoinList` ' A gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.

## <a name="configuration-and-settings"></a>Yapılandırma ve ayarlar

Gelişmiş seçenekleri yapılandırmak için, MVC Oluşturucu 'da genişletme `AddRazorPagesOptions` yöntemini kullanın:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Şu anda ' nı, `RazorPagesOptions` sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz. Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.

Görünümleri önceden derlemek için bkz [ Razor . derlemeyi görüntüle](xref:mvc/views/view-compilation) .

[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Razor Sayfaların içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor sayfalar, */Pages* dizininde kök olarak depolanır. Razor Sayfalarınızın, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) ([contentrootpath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) olduğunu belirtmek için [addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ' ye [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) ekleyin:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Razor Sayfaların özel kök dizinde olduğunu belirtme

Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye WithRazorPagesRoot ekleyin (göreli bir yol sağlayın): [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) Razor

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
