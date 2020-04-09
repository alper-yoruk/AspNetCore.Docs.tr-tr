---
title: ASP.NET Core'da Jilet Sayfalarına Giriş
author: Rick-Anderson
description: ASP.NET Core'daki Razor Pages'in sayfa odaklı senaryoları kodlamayı MVC kullanmaktan nasıl daha kolay ve üretken hale getirir hale ettiğini öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667582"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>ASP.NET Core'da Jilet Sayfalarına Giriş

::: moniker range=">= aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan Nowak](https://github.com/rynowak)

Razor Pages, sayfa odaklı senaryoları kodlamayı denetleyicileri ve görünümleri kullanmaktan daha kolay ve üretken hale getirebilir.

Model-Görünüm-Denetleyici yaklaşımını kullanan bir öğretici arıyorsanız, ASP.NET [bkz.](xref:tutorials/first-mvc-app/start-mvc)

Bu belge, Razor Pages için bir giriş sağlar. Bu adım öğretici bir adım değil. Bazı bölümleri çok gelişmiş bulursanız, [bkz.](xref:tutorials/razor-pages/razor-pages-start) ASP.NET Core'a genel bakış için [ASP.NET Çekirdeğine Giriş bölümüne](xref:index)bakın.

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Jilet Sayfaları projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut `dotnet new webapp` satırından çalıştırın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut `dotnet new webapp` satırından çalıştırın.

Mac için Visual Studio'dan oluşturulan *.csproj* dosyasını açın.

---

## <a name="razor-pages"></a>Razor Pages

Jilet Sayfaları *Startup.cs*olarak etkinleştirilir:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Önceki kod denetleyicileri ve görünümleri ile ASP.NET Core uygulamasında kullanılan bir [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) na çok benzer. Onu farklı kılan [`@page`](xref:mvc/views/razor#page) direktiftir. `@page`dosyayı bir MVC eylemine dönüştürür - bu da bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`bir sayfadaki ilk Razor direktifi olmalıdır. `@page`diğer [Razor](xref:mvc/views/razor) yapılarının davranışını etkiler. Razor Pages dosya adlarında *.cshtml* soneki vardır.

Benzer bir sayfa, `PageModel` bir sınıf kullanarak, aşağıdaki iki dosya gösterilir. *Sayfalar/Index2.cshtml* dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Sayfalar/Index2.cshtml.cs* sayfa modeli:

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Sözleşmeye `PageModel` göre, sınıf dosyası *.cs* eklenen Razor Page dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor Page *Pages/Index2.cshtml*olduğunu. `PageModel` Sınıfı içeren dosya *Sayfalar/Index2.cshtml.cs*olarak adlandırılır.

URL yollarının sayfalara çağrışımları, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda Bir Jilet Sayfası yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yol               | eşleşen URL |
| ----------------- | ------------ |
| */Sayfalar/Index.cshtml* | `/` veya `/Index` |
| */Sayfalar/İletişim.cshtml* | `/Contact` |
| */Sayfalar/Mağaza/İletişim.cshtml* | `/Store/Contact` |
| */Sayfalar/Mağaza/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı varsayılan olarak *Sayfalar* klasöründe Razor Pages dosyalarını arar.
* `Index`bir URL sayfa içermediği varsayılan sayfadır.

## <a name="write-a-basic-form"></a>Temel bir form yazma

Razor Pages, bir uygulama kurarken web tarayıcılarıyla kullanılan yaygın desenleri kolayca uygulamak için tasarlanmıştır. [Model bağlama,](xref:mvc/models/model-binding) [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML yardımcıları, tüm bunlar jilet sayfası sınıfında tanımlanan özelliklerle *çalışır.* `Contact` Model için temel bir "bize ulaşın" formu uygulayan bir sayfa düşünün:

Bu belgedeki örnekler `DbContext` [için, Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başharf.

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Veri modeli:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

DB bağlamı:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

*Pages/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

*Sayfalar/Create.cshtml.cs* sayfa modeli:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Kural olarak, `PageModel` sınıf `<PageName>Model` çağrılır ve sayfayla aynı ad alanındadır.

Sınıf, `PageModel` bir sayfanın mantığının sunusundan ayrılmasına izin verir. Sayfaya gönderilen istekler ve sayfayı işlemek için kullanılan veriler için sayfa işleyicilerini tanımlar. Bu ayırma sağlar:

* [Bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarının yönetilmesi.
* [Birim testi](xref:test/razor-pages-tests)

Sayfada istekler `OnPostAsync` üzerinde `POST` çalışan bir *işleyici yöntemi*vardır (kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici yöntemleri eklenebilir. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. Önceki kodda, `OnGet` yöntem *CreateModel.cshtml* Razor Sayfasını görüntüler.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Önceki kod, Razor Pages için tipiktir.

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamalara aşinaysanız:

* Önceki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer görünüyor.
* [Model bağlama,](xref:mvc/models/model-binding) [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi MVC ilkel çoğu Denetleyicileri ve Jilet Sayfaları ile aynı çalışır. 

Önceki `OnPostAsync` yöntem:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel `OnPostAsync`akış:

Doğrulama hatalarını denetleyin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, sayfayı doğrulama iletileriyle yeniden gösterin. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

*Pages/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

*Pages/Create.cshtml*adresinden işlenen HTML :

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Önceki kodda, formu deftere nakletmek:

* Geçerli verilerle:

  * Işleyici `OnPostAsync` yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemi çağırır. `RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür. `RedirectToPage`:

    * Bir eylem sonucudur.
    * Benzer `RedirectToAction` veya `RedirectToRoute` (denetleyicileri ve görünümleri kullanılır).
    * Sayfalar için özelleştirilmiştir. Önceki örnekte, kök Dizin sayfasına yönlendirir`/Index`( ). `RedirectToPage`Sayfalar için [URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

* Sunucuya geçirilen doğrulama hatalarıyla:

  * Işleyici `OnPostAsync` yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemi çağırır. `Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür. İade, `Page` denetleyicideki eylemlerin nasıl geri `View`döndüğüne benzer. `PageResult`işleyici yöntemi için varsayılan dönüş türüdür. Sayfayı döndüren `void` bir işleyici yöntemi.
  * Önceki örnekte, [modelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış dönen hiçbir değer sonuçları ile form gönderme. Bu örnekte, istemcide doğrulama hatası görüntülenmez. Doğrulama hatası teslimi daha sonra bu belgede ele alınmıştır.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* İstemci tarafı doğrulama tarafından algılanan doğrulama hataları ile:

  * Veriler sunucuya **nakledilmez.**
  * İstemci tarafı doğrulaması daha sonra bu belgede açıklanır.

Özellik, `Customer` [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) bağlamayı modellemeyi tercih etmek için öznitelik kullanır:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikleri içeren modellerde **kullanılmamalıdır.** Daha fazla bilgi için [bkz.](xref:data/ef-rp/crud#overposting)

Razor Pages, varsayılan olarak, yalnızca`GET` olmayan fiillerle özellikleri bağlar. Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma gereksinimini ortadan kaldırır. Bağlama, form alanlarını işlemek için aynı`<input asp-for="Customer.Name">`özelliği kullanarak kodu azaltır ( ) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

*Pages/Create.cshtml* görünüm dosyasını gözden geçirme:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Önceki kodda, [giriş etiketi yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini model ifadesine `Customer.Name` bağlar.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Tag Yardımcıları kullanılabilir hale getirir.

### <a name="the-home-page"></a>Ana sayfa

*Index.cshtml* ana sayfadır:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

İlişkili `PageModel` sınıf (*Index.cshtml.cs):*

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

*Index.cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Çapa Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-route-{value}` Edit sayfasına bir bağlantı oluşturmak için özniteliği kullandı. Bağlantı, ilgili kişi kimliğiyle birlikte rota verilerini içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir.

*Index.cshtml* dosyası, her müşteri ilgili kişi için bir silme düğmesi oluşturmak için işaretleme içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

İşlenen HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Silme düğmesi HTML'de işlendiğinde, [biçimeylemi](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) aşağıdakiparametreleri içerir:

* Öznitelik tarafından `asp-route-id` belirtilen müşteri iletişim kimliği.
* Öznitelik `handler`tarafından `asp-page-handler` belirtilir.

Düğme seçildiğinde, sunucuya `POST` bir form isteği gönderilir. Kural kuralına göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.

Bu `handler` örnekte olduğundan, `delete` `OnPostDeleteAsync` `POST` işleyici yöntemi isteği işlemek için kullanılır. Farklı `asp-page-handler` bir değere ayarlanmışsa, `remove`örneğin , adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Yöntem: `OnPostDeleteAsync`

* Sorgu `id` dizesinden alır.
* Müşteri ile ilgili iletişim için `FindAsync`veritabanını sorgular.
* Müşteri ilgili kişi bulunursa, kaldırılır ve veritabanı güncelleştirilir.
* Kök <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> Dizin sayfasına yönlendirmek`/Index`için çağrılar ( ).

### <a name="the-editcshtml-file"></a>Edit.cshtml dosyası

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

İlk satır yönergeyi `@page "{id:int}"` içerir. Yönlendirme kısıtlaması,`"{id:int}"` sayfaya rota verilerini içeren `int` sayfadaki istekleri kabul etmesini söyler. Sayfaya yapılan bir `int`istek, bir ,çalışma zamanı http 404 (bulunamadı) hatasına dönüştürülebilecek rota verileri içermiyorsa. Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:

 ```cshtml
@page "{id:int?}"
```

Edit.cshtml.cs *Edit.cshtml.cs* dosyası:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Doğrulama

Doğrulama kuralları:

* Model sınıfında bildirimsel olarak belirtilir.
* Uygulamanın her yerinde uygulanır.

Ad <xref:System.ComponentModel.DataAnnotations> alanı, bir sınıfa veya özelliğe bildirimsel olarak uygulanan yerleşik doğrulama öznitelikleri kümesi sağlar. DataAnnotations da biçimlendirme [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) ile yardımcı gibi biçimlendirme öznitelikleri içerir ve herhangi bir doğrulama sağlamaz.

Modeli `Customer` göz önünde bulundurun:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Aşağıdaki *Create.cshtml* görünüm dosyasını kullanma:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Yukarıdaki kod:

* jQuery ve jQuery doğrulama komut dosyalarını içerir.
* Etkinleştirmek `<div />` `<span />` için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:

  * İstemci tarafı doğrulaması.
  * Doğrulama hatası oluşturma.

* Aşağıdaki HTML'yi oluşturur:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Ad değeri olmadan Form Oluşturma'da hata iletisi görüntülenir"Ad alanı gereklidir." şeklinde. İstemcide JavaScript etkinleştirilmişse, tarayıcı hatayı sunucuya göndermeden görüntüler.

Öznitelik `[StringLength(10)]` işlenen `data-val-length-max="10"` HTML üzerinde oluşturur. `data-val-length-max`tarayıcıların belirtilen maksimum uzunluktan daha fazla girmesini engeller. Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılırsa:

* Adı 10'dan uzun.
* Hata iletisi "Alan Adı en fazla 10 uzunluğa sahip bir dize olmalıdır." döndürülür.

Aşağıdaki `Movie` modeli göz önünde bulundurun:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulandıkları model özellikleriüzerinde uygulanacak davranışı belirtir:

* Ve `Required` `MinimumLength` öznitelikleri bir özelliğin bir değeri olması gerektiğini gösterir, ancak hiçbir şey bu doğrulamayı karşılamak için beyaz boşluk girmesini kullanıcı engeller.
* Öznitelik, `RegularExpression` hangi karakterlerin giriş olabileceğini sınırlamak için kullanılır. Önceki kodda, "Tür":

  * Sadece harfleri kullanmalısınız.
  * İlk harfin büyük harf olması gerekir. Beyaz boşluk, sayılar ve özel karakterlere izin verilmez.

* `RegularExpression` "Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki alanlarda özel karakterlere ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "Tür" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* Öznitelik, `StringLength` dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en az uzunluğunu ayarlar.
* Değer türleri (, `int` `float`, `DateTime`, , ) doğal `[Required]` `decimal`olarak gereklidir ve öznitelik gerekmez.

Modeliçin `Movie` Oluştur sayfası geçersiz değerlere sahip hataları gösterir:

![Birden çok jQuery istemci tarafı doğrulama hatası olan film görüntüleme formu](~/tutorials/razor-pages/validation/_static/val.png)

Daha fazla bilgi için bkz.

* [Film uygulamasına doğrulama ekleme](xref:tutorials/razor-pages/validation)
* [ASP.NET Core'da model doğrulama.](xref:mvc/models/validation)

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşle HEAD isteklerini işleme

`HEAD`istekleri belirli bir kaynak için üstbilgi alma sağlar. İsteklerden `GET` `HEAD` farklı olarak, istekler yanıt gövdesi döndürmez.

Normalde, bir `OnHead` işleyici oluşturulur ve istekler için `HEAD` çağrılır:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages, işleyici `OnGet` tanımlanmamışsa `OnHead` işleyiciyi aramaya geri döner.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF ve Jilet Sayfaları

Jilet Sayfaları [Antiforgery doğrulama](xref:security/anti-request-forgery)ile korunmaktadır. [FormTagHelper,](xref:mvc/views/working-with-forms#the-form-tag-helper) HTML form öğelerine antiforgery belirteçleri enjekte eder.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Düzenleri, kısmileri, şablonları ve Yardımcıları Jilet Li Sayfalarla Etiketleme

Sayfalar, Razor görünüm motorunun tüm özellikleriyle çalışır. Düzenleri, partials, şablonlar, Tag Helpers, *_ViewStart.cshtml*ve *_ViewImports.cshtml* geleneksel Razor görünümleri için yaptıkları gibi çalışır.

Bu özelliklerden bazılarını yararlanarak bu sayfayı declutter edelim.

*Sayfalara/Paylaşılan/_Layout.cshtml'e* [düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın düzenini denetler (sayfa mizanpajı devre dışı bırakmadığı sürece).
* JavaScript ve stylesheets gibi HTML yapılarını içeri aktlar.
* Razor sayfasının içeriği, çağrıldığı `@RenderBody()` yerde işlenir.

Daha fazla bilgi için [düzen sayfasına](xref:mvc/views/layout)bakın.

[Düzen](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart.cshtml*olarak ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/Paylaşılanklasöründedir.* Sayfalar, geçerli sayfayla aynı klasörden başlayarak hiyerarşik olarak diğer görünümleri (düzenler, şablonlar, kısmiler) arar. *Sayfalar/Paylaşılan* klasöründeki bir *düzen, Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.

Düzen dosyası *Sayfalar/Paylaşılan* klasörüne gitmeli.

Düzen dosyasını *Görünümler/Paylaşılanklasörüne* **koymamanızı** öneririz. *Görünümler/Paylaşılanlar* bir MVC görünüm desenidir. Razor Pages, yol kurallarına değil, klasör hiyerarşisine güvenmek içindir.

Razor Page'deki aramayı *görüntüleyin, Sayfalar* klasörünü içerir. MVC denetleyicileri ve geleneksel Razor görünümleri ile kullanılan düzenler, şablonlar ve kısmi ler *yalnızca çalışır.*

*Pages/_ViewImports.cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`öğreticidaha sonra açıklanmıştır. Yönerge, `@addTagHelper` yerleşik Tag *Yardımcıları'nı Sayfalar* klasöründeki tüm sayfalara getirir. [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index)

<a name="namespace"></a>

Bir `@namespace` sayfada ayarlanan yönerge:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Yönerge, `@namespace` sayfanın ad alanını ayarlar. Yönerge, `@model` ad alanını içermemelidir.

Yönerge *_ViewImports.cshtml'de*yer aldığında, belirtilen ad `@namespace` alanı, yönergeyi içeri yediren Sayfada oluşturulan ad alanı için öneki sağlar. `@namespace` Oluşturulan ad alanının geri kalanı (sonek bölümü), *_ViewImports.cshtml* içeren klasör ile sayfayı içeren klasör arasındaki nokta-ayrılmış göreli yoldur.

Örneğin, `PageModel` Sınıf *Pages/Customers/Edit.cshtml.cs* açıkça ad alanını ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports.cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Sayfalar/Müşteriler/Edit.cshtml* Razor Page için oluşturulan ad alanı `PageModel` sınıfla aynıdır.

`@namespace`*ayrıca geleneksel Razor görünümleri ile çalışır.*

*Sayfalar/Create.cshtml* görünüm dosyasını göz önünde bulundurun:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

*_ViewImports.cshtml* ve önceki düzen dosyası ile güncelleştirilmiş *Pages/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Önceki kodda, *_ViewImports.cshtml* ad alanı ve Tag Helpers'ı aktarDı. Düzen dosyası JavaScript dosyalarını içe aktardı.

[Razor Pages başlangıç projesi,](#rpvs17) istemci tarafı doğrulaması kancasayfalar/_ValidationScriptsPartial.cshtml içerir. *Pages/_ValidationScriptsPartial.cshtml*

Kısmi görünümler hakkında daha <xref:mvc/views/partial>fazla bilgi için bkz.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

Daha `Create` önce gösterilen sayfa `RedirectToPage`yı kullanır:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Sayfalar*

  * *Index.cshtml*
  * *Gizlilik.cshtml*
  * */Müşteriler*

    * *Oluştur.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Sayfalar/Müşteriler/Create.cshtml* ve *Pages/Customers/Edit.cshtml* sayfaları başarıdan sonra *Sayfalar/Müşteriler/Index.cshtml'e* yönlendirir. Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır. *Sayfalar/Müşteriler/Index.cshtml* sayfasına URL oluşturmak için kullanılır. Örneğin:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Mutlak sayfa `/Index` *adı, Pages/Index.cshtml* sayfasına URL oluşturmak için kullanılır. Örneğin:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, bir satır aralığı `/` (örneğin, `/Index`) dahil olmak üzere kök */Sayfalar* klasöründen sayfaya giden yoldur. Önceki URL oluşturma örnekleri, bir URL'yi sıkı kodlamaya göre gelişmiş seçenekler ve işlevsel özellikler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve rotanın hedef yolda nasıl tanımlandığına göre parametreler oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, `RedirectToPage` *Sayfalar/Müşteriler/Create.cshtml'de*farklı parametreler kullanılarak hangi Dizin sayfasının seçildiği gösterilmektedir.

| YönlendirmePage(x)| Sayfa |
| ----------------- | ------------ |
| ReDirectToPage("/Index") | *Sayfalar/Dizin* |
| ReDirectToPage("./Index"); | *Sayfalar/Müşteriler/Dizin* |
| YönlendirmePage("... /Index") | *Sayfalar/Dizin* |
| ReDirectToPage("Index")  | *Sayfalar/Müşteriler/Dizin* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` *göreli adlar*. Parametre, `RedirectToPage` hedef sayfanın adını hesaplamak için geçerli sayfanın yolu ile *birleştirilir.*

Göreli ad bağlama, karmaşık bir yapıya sahip siteleri inşa ederken yararlıdır. Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanıldığında:

* Bir klasörü yeniden adlandırmak göreli bağlantıları kesmez.
* Klasör adını içermedikleri için bağlantılar kırılmaz.

Farklı bir [Alan'daki](xref:mvc/controllers/areas)bir sayfaya yönlendirmek için alanı belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veriler <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>bir sayfaya iletilebilir. Öznitelik `[ViewData]` teki özelliklerin değerleri depolanır ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>'den yüklenir.

Aşağıdaki örnekte, `AboutModel` `[ViewData]` `Title` özelliğe öznitelik uygular:

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

Hakkında sayfasında, `Title` özellik bir model özelliği olarak erişin:

```cshtml
<h1>@Model.Title</h1>
```

Düzende, başlık ViewData sözlüğünden okunur:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>Geçici Veriler

ASP.NET Core ortaya <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>çıkarır . Bu özellik, verileri okunana kadar depolar. Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silme olmadan verileri incelemek için kullanılabilir. `TempData`tek bir istekten daha fazlası için veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod `Message` kullanma `TempData`değerini ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Sayfalar/Müşteriler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirme, `Message` kullanmanın `TempData`değerini görüntüler.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Sayfalar/Müşteriler/Index.cshtml.cs* sayfa modeli `[TempData]` `Message` özelliğiöznitelik uygular.

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için [TempData'ya](xref:fundamentals/app-state#tempdata)bakın.

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, `asp-page-handler` Etiket Yardımcısı'nı kullanarak iki işleyici için biçimlendirme oluşturur:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Önceki örnekteki formun her biri farklı bir `FormActionTagHelper` URL'ye göndermek için kullanılan iki gönderme düğmesi vardır. Öznitelik `asp-page-handler` bir `asp-page`arkadaşıdır. `asp-page-handler`bir sayfa tarafından tanımlanan işleyici yöntemlerinin her birine gönderen URL'ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığı için belirtilmemiştir.

Sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Önceki kod *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, (varsa) sonra `On<HTTP Verb>` ve `Async` öncesinde ki addaki metin alınarak oluşturulur. Önceki örnekte, sayfa yöntemleri OnPost**JoinList**Async ve OnPost**JoinListUC**Async'dir. *OnPost* ve *Async* kaldırıldı, işleyici `JoinList` `JoinListUC`adları ve .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH?handler=JoinList` Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Özel yollar

Yönergeyi `@page` şu şekilde kullanın:

* Sayfaya özel bir rota belirtin. Örneğin, Hakkında sayfasına `/Some/Other/Path` giden yol . `@page "/Some/Other/Path"`
* Bir sayfanın varsayılan rotasına segmentleri ekleyin. Örneğin, bir "öğe" kesimi sayfanın varsayılan rotasına `@page "item"`.
* Parametreleri sayfanın varsayılan rotasına ekleyin. Örneğin, bir kimlik parametresi, `id`, . `@page "{id}"`

Yolun başında ki bir tilde`~`( ) tarafından belirlenen kök-göreli yol desteklenir. Örneğin, `@page "~/Some/Other/Path"` aynı `@page "/Some/Other/Path"`.

URL'deki sorgu dizesini `?handler=JoinList` beğenmezseniz, URL'nin yol bölümüne işleyici adını koymak için rotayı değiştirin. Rota, `@page` yönergeden sonra çift tırnak içinde bir rota şablonu eklenerek özelleştirilebilir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH/JoinList` Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` Aşağıdaki, `handler` rota parametresi isteğe bağlı olduğu anlamına gelir.

## <a name="advanced-configuration-and-settings"></a>Gelişmiş yapılandırma ve ayarlar

Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama tarafından gerekli değildir.

Gelişmiş seçenekleri yapılandırmak için uzantı yöntemini <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>kullanın:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Sayfalar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> için kök dizinini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanın. Kongreler hakkında daha fazla bilgi [için, Bkz. Razor Pages yetkilendirme kuralları.](xref:security/authorization/razor-pages-authorization)

Görünümleri önceden derlemek [için, Bkz. Razor görünüm](xref:mvc/views/view-compilation)derlemesi.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Razor Sayfalarının içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor Pages */Pages* dizininde köklenir. Razor <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> Sayfalarınızın uygulamanın içerik [kökünde](xref:fundamentals/index#content-root) <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>( ) olduğunu belirtmek için ekleyin:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Razor Sayfalarının özel bir kök dizininde olduğunu belirtin

Razor <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> Pages'in uygulamada özel bir kök dizininde olduğunu belirtmek için ekleyin (göreceli bir yol sağlayın):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* Bu giriş üzerine inşa [Razor Pages ile başlayın](xref:tutorials/razor-pages/razor-pages-start)bakın
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

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan Nowak](https://github.com/rynowak)

Razor Pages, ASP.NET Core MVC'nin sayfa odaklı senaryoları kodlamayı daha kolay ve üretken hale getiren yeni bir yönüdür.

Model-Görünüm-Denetleyici yaklaşımını kullanan bir öğretici arıyorsanız, ASP.NET [bkz.](xref:tutorials/first-mvc-app/start-mvc)

Bu belge, Razor Pages için bir giriş sağlar. Bu adım öğretici bir adım değil. Bazı bölümleri çok gelişmiş bulursanız, [bkz.](xref:tutorials/razor-pages/razor-pages-start) ASP.NET Core'a genel bakış için [ASP.NET Çekirdeğine Giriş bölümüne](xref:index)bakın.

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Jilet Sayfaları projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Razor Pages projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut `dotnet new webapp` satırından çalıştırın.

Mac için Visual Studio'dan oluşturulan *.csproj* dosyasını açın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut `dotnet new webapp` satırından çalıştırın.

---

## <a name="razor-pages"></a>Razor Pages

Jilet Sayfaları *Startup.cs*olarak etkinleştirilir:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Önceki kod denetleyicileri ve görünümleri ile ASP.NET Core uygulamasında kullanılan bir [Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) na çok benzer. Onu farklı kılan `@page` direktiftir. `@page`dosyayı bir MVC eylemine dönüştürür - bu da bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`bir sayfadaki ilk Razor direktifi olmalıdır. `@page`diğer Razor yapılarının davranışını etkiler.

Benzer bir sayfa, `PageModel` bir sınıf kullanarak, aşağıdaki iki dosya gösterilir. *Sayfalar/Index2.cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Sayfalar/Index2.cshtml.cs* sayfa modeli:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Sözleşmeye `PageModel` göre, sınıf dosyası *.cs* eklenen Razor Page dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor Page *Pages/Index2.cshtml*olduğunu. `PageModel` Sınıfı içeren dosya *Sayfalar/Index2.cshtml.cs*olarak adlandırılır.

URL yollarının sayfalara çağrışımları, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda Bir Jilet Sayfası yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yol               | eşleşen URL |
| ----------------- | ------------ |
| */Sayfalar/Index.cshtml* | `/` veya `/Index` |
| */Sayfalar/İletişim.cshtml* | `/Contact` |
| */Sayfalar/Mağaza/İletişim.cshtml* | `/Store/Contact` |
| */Sayfalar/Mağaza/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı varsayılan olarak *Sayfalar* klasöründe Razor Pages dosyalarını arar.
* `Index`bir URL sayfa içermediği varsayılan sayfadır.

## <a name="write-a-basic-form"></a>Temel bir form yazma

Razor Pages, bir uygulama kurarken web tarayıcılarıyla kullanılan yaygın desenleri kolayca uygulamak için tasarlanmıştır. [Model bağlama,](xref:mvc/models/model-binding) [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML yardımcıları, tüm bunlar jilet sayfası sınıfında tanımlanan özelliklerle *çalışır.* `Contact` Model için temel bir "bize ulaşın" formu uygulayan bir sayfa düşünün:

Bu belgedeki örnekler `DbContext` [için, Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başharf.

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Veri modeli:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

DB bağlamı:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

*Pages/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

*Sayfalar/Create.cshtml.cs* sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Kural olarak, `PageModel` sınıf `<PageName>Model` çağrılır ve sayfayla aynı ad alanındadır.

Sınıf, `PageModel` bir sayfanın mantığının sunusundan ayrılmasına izin verir. Sayfaya gönderilen istekler ve sayfayı işlemek için kullanılan veriler için sayfa işleyicilerini tanımlar. Bu ayırma sağlar:

* [Bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarının yönetilmesi.
* Ünite sayfaları [test ediyor.](xref:test/razor-pages-tests)

Sayfada istekler `OnPostAsync` üzerinde `POST` çalışan bir *işleyici yöntemi*vardır (kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. [OnGet](#OnGet) örnek.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Önceki kod, Razor Pages için tipiktir.

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamalara aşinaysanız:

* Önceki `OnPostAsync` örnekteki kod, tipik denetleyici koduna benzer görünüyor.
* [Model bağlama,](xref:mvc/models/model-binding) [doğrulama,](xref:mvc/models/validation) [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi MVC ilkel lerin çoğu paylaşılır.

Önceki `OnPostAsync` yöntem:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel `OnPostAsync`akış:

Doğrulama hatalarını denetleyin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, sayfayı doğrulama iletileriyle yeniden gösterin. İstemci tarafı doğrulama, geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için `RedirectToPageResult`yardımcı yöntemi çağırır. `RedirectToPage`yeni bir eylem sonucudur, benzer `RedirectToAction` veya `RedirectToRoute`, ancak sayfalar için özelleştirilmiş. Önceki örnekte, kök Dizin sayfasına yönlendirir`/Index`( ). `RedirectToPage`Sayfalar için [URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

Gönderilen formda doğrulama hataları (sunucuya geçirilen) olduğunda,`OnPostAsync` işleyici `Page` yöntemi yardımcı yöntemi çağırır. `Page`, bir `PageResult` örneği döndürür. İade, `Page` denetleyicideki eylemlerin nasıl geri `View`döndüğüne benzer. `PageResult`işleyici yöntemi için varsayılan dönüş türüdür. Sayfayı döndüren `void` bir işleyici yöntemi.

Özellik, `Customer` `[BindProperty]` bağlamayı modellemek için öznitelik kullanır.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages, varsayılan olarak, yalnızca`GET` olmayan fiillerle özellikleri bağlar. Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir. Bağlama, form alanlarını işlemek için aynı`<input asp-for="Customer.Name">`özelliği kullanarak kodu azaltır ( ) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

Ana sayfa (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

İlişkili `PageModel` sınıf (*Index.cshtml.cs):*

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index.cshtml* dosyası, her kişi için bir edit bağlantısı oluşturmak için aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Çapa Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-route-{value}` Edit sayfasına bir bağlantı oluşturmak için özniteliği kullandı. Bağlantı, ilgili kişi kimliğiyle birlikte rota verilerini içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), Razor dosyalarında HTML öğelerinin oluşturulmasına ve işlenmesine sunucu tarafı kodun katılmasını etkinleştir. Tag Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

*Sayfalar/Edit.cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

İlk satır yönergeyi `@page "{id:int}"` içerir. Yönlendirme kısıtlaması,`"{id:int}"` sayfaya rota verilerini içeren `int` sayfadaki istekleri kabul etmesini söyler. Sayfaya yapılan bir `int`istek, bir ,çalışma zamanı http 404 (bulunamadı) hatasına dönüştürülebilecek rota verileri içermiyorsa. Kimliği isteğe bağlı hale `?` getirmek için rota kısıtlamasına eklenen:

 ```cshtml
@page "{id:int?}"
```

*Sayfalar/Edit.cshtml.cs* dosyası:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

*Index.cshtml* dosyası, her müşteri ilgili kişi için bir silme düğmesi oluşturmak için işaretleme de içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Silme düğmesi HTML'de işlendiğinde, `formaction` aşağıdakiparametreleri içerir:

* Öznitelik tarafından `asp-route-id` belirtilen müşteri iletişim kimliği.
* Öznitelik `handler` tarafından `asp-page-handler` belirtilen.

Burada bir müşteri iletişim kimliği ile işlenen silme `1`düğmesine bir örnek:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Düğme seçildiğinde, sunucuya `POST` bir form isteği gönderilir. Kural kuralına göre, işleyici yönteminin adı, şemaya `handler` `OnPost[handler]Async`göre parametrenin değerine göre seçilir.

Bu `handler` örnekte olduğundan, `delete` `OnPostDeleteAsync` `POST` işleyici yöntemi isteği işlemek için kullanılır. Farklı `asp-page-handler` bir değere ayarlanmışsa, `remove`örneğin , adında `OnPostRemoveAsync` bir işleyici yöntemi seçilir. Aşağıdaki kod işleyiciyi `OnPostDeleteAsync` gösterir:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Yöntem: `OnPostDeleteAsync`

* Sorgu dizesinden kabul `id` eder. *Index.cshtml* sayfa yönergesi yönlendirme `"{id:int?}"` `id` kısıtlaması içeriyorsa, rota verilerinden gelir. Bunun için `id` rota verileri URI'de `https://localhost:5001/Customers/2`belirtilir.
* Müşteri ile ilgili iletişim için `FindAsync`veritabanını sorgular.
* Müşteri ilgili kişi bulunursa, müşteri ilgili kişiler listesinden kaldırılır. Veritabanı güncelleştirildi.
* Kök `RedirectToPage` Dizin sayfasına yönlendirmek`/Index`için çağrılar ( ).

## <a name="mark-page-properties-as-required"></a>Sayfa özelliklerini gerektiği gibi işaretleme

A'daki `PageModel` özellikler [Gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelik ile işaretlenebilir:

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Daha fazla bilgi için [Model doğrulamaya](xref:mvc/models/validation)bakın.

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşle HEAD isteklerini işleme

`HEAD`istekler, belirli bir kaynak için üstbilgi almanızı sağlar. İsteklerden `GET` `HEAD` farklı olarak, istekler yanıt gövdesi döndürmez.

Normalde, bir `OnHead` işleyici oluşturulur ve istekler için `HEAD` çağrılır: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

Core 2.1 veya daha sonraki ASP.NET, Razor `OnGet` Pages hiçbir `OnHead` işleyici tanımlı ise işleyici aramaya geri düşer. Bu davranış, [SetCompatibilityVersion](xref:mvc/compatibility-version) için çağrı `Startup.ConfigureServices`tarafından etkinleştirilir:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Varsayılan şablonlar çağrıyı `SetCompatibilityVersion` Core 2.1 ve 2.2'ASP.NET oluşturur. `SetCompatibilityVersion`razor Pages seçeneğini `AllowMappingHeadRequestsToGetHandler` etkin `true`bir şekilde ayarlar.

Tüm davranışları seçerek tercih etmek `SetCompatibilityVersion`yerine, *açıkça belirli* davranışları tercih edebilirsiniz. Aşağıdaki kod, isteklerin `HEAD` `OnGet` işleyiciye eşlenemesine izin vermeyi seçer:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF ve Jilet Sayfaları

[Antiforgery doğrulama](xref:security/anti-request-forgery)için herhangi bir kod yazmak zorunda değilsiniz. Antiforgery belirteç oluşturma ve doğrulama otomatik olarak Razor Pages dahildir.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Düzenleri, kısmileri, şablonları ve Yardımcıları Jilet Li Sayfalarla Etiketleme

Sayfalar, Razor görünüm motorunun tüm özellikleriyle çalışır. Düzenleri, partials, şablonlar, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* geleneksel Razor görünümleri için yaptıkları gibi çalışır.

Bu özelliklerden bazılarını yararlanarak bu sayfayı declutter edelim.

*Sayfalara/Paylaşılan/_Layout.cshtml'e* [düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın düzenini denetler (sayfa mizanpajı devre dışı bırakmadığı sürece).
* JavaScript ve stylesheets gibi HTML yapılarını içeri aktlar.

Daha fazla bilgi için [düzen sayfasına](xref:mvc/views/layout) bakın.

[Düzen](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart.cshtml*olarak ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/Paylaşılanklasöründedir.* Sayfalar, geçerli sayfayla aynı klasörden başlayarak hiyerarşik olarak diğer görünümleri (düzenler, şablonlar, kısmiler) arar. *Sayfalar/Paylaşılan* klasöründeki bir *düzen, Sayfalar* klasörü altındaki herhangi bir Razor sayfasından kullanılabilir.

Düzen dosyası *Sayfalar/Paylaşılan* klasörüne gitmeli.

Düzen dosyasını *Görünümler/Paylaşılanklasörüne* **koymamanızı** öneririz. *Görünümler/Paylaşılanlar* bir MVC görünüm desenidir. Razor Pages, yol kurallarına değil, klasör hiyerarşisine güvenmek içindir.

Razor Page'deki aramayı *görüntüleyin, Sayfalar* klasörünü içerir. MVC denetleyicileri ve geleneksel Razor görünümleri ile kullandığınız mizanpajlar, şablonlar ve kısmi ler *yalnızca çalışır.*

*Pages/_ViewImports.cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`öğreticidaha sonra açıklanmıştır. Yönerge, `@addTagHelper` yerleşik Tag *Yardımcıları'nı Sayfalar* klasöründeki tüm sayfalara getirir. [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index)

<a name="namespace"></a>

`@namespace` Yönerge bir sayfada açıkça kullanıldığında:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Yönerge, sayfanın ad alanını ayarlar. Yönerge, `@model` ad alanını içermemelidir.

Yönerge *_ViewImports.cshtml'de*yer aldığında, belirtilen ad `@namespace` alanı, yönergeyi içeri yediren Sayfada oluşturulan ad alanı için öneki sağlar. `@namespace` Oluşturulan ad alanının geri kalanı (sonek bölümü), *_ViewImports.cshtml* içeren klasör ile sayfayı içeren klasör arasındaki nokta-ayrılmış göreli yoldur.

Örneğin, `PageModel` Sınıf *Pages/Customers/Edit.cshtml.cs* açıkça ad alanını ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports.cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Sayfalar/Müşteriler/Edit.cshtml* Razor Page için oluşturulan ad alanı `PageModel` sınıfla aynıdır.

`@namespace`*ayrıca geleneksel Razor görünümleri ile çalışır.*

Özgün *Pages/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Güncelleştirilmiş *Sayfalar/Create.cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[Razor Pages başlangıç projesi,](#rpvs17) istemci tarafı doğrulaması kancasayfalar/_ValidationScriptsPartial.cshtml içerir. *Pages/_ValidationScriptsPartial.cshtml*

Kısmi görünümler hakkında daha <xref:mvc/views/partial>fazla bilgi için bkz.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

Daha `Create` önce gösterilen sayfa `RedirectToPage`yı kullanır:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Sayfalar*

  * *Index.cshtml*
  * */Müşteriler*

    * *Oluştur.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

*Sayfalar/Müşteriler/Create.cshtml* ve *Pages/Customers/Edit.cshtml* sayfaları başarıdan sonra *Pages/Index.cshtml'e* yönlendirilir. Dize, `/Index` önceki sayfaya erişmek için URI'nin bir parçasıdır. `/Index` *Dize, Pages/Index.cshtml* sayfasına ÜRBİ oluşturmak için kullanılabilir. Örneğin:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, bir satır aralığı `/` (örneğin, `/Index`) dahil olmak üzere kök */Sayfalar* klasöründen sayfaya giden yoldur. Önceki URL oluşturma örnekleri, bir URL'yi kodlamakonusunda gelişmiş seçenekler ve işlevsel özellikler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve rotanın hedef yolda nasıl tanımlandığına göre parametreler oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, `RedirectToPage` *Sayfalar/Müşteriler/Create.cshtml*farklı parametrelerle hangi Dizin sayfasının seçildiği gösterilmektedir:

| YönlendirmePage(x)| Sayfa |
| ----------------- | ------------ |
| ReDirectToPage("/Index") | *Sayfalar/Dizin* |
| ReDirectToPage("./Index"); | *Sayfalar/Müşteriler/Dizin* |
| YönlendirmePage("... /Index") | *Sayfalar/Dizin* |
| ReDirectToPage("Index")  | *Sayfalar/Müşteriler/Dizin* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`ve `RedirectToPage("../Index")` *göreli adlar*. Parametre, `RedirectToPage` hedef sayfanın adını hesaplamak için geçerli sayfanın yolu ile *birleştirilir.*  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Göreli ad bağlama, karmaşık bir yapıya sahip siteleri inşa ederken yararlıdır. Bir klasördeki sayfalar arasında bağlantı vermek için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz. Tüm bağlantılar hala çalışır (klasör adını içermedikleri için).

Farklı bir [Alan'daki](xref:mvc/controllers/areas)bir sayfaya yönlendirmek için alanı belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veriler [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)ile bir sayfaya geçirilebilir. Öznitelikile `[ViewData]` denetleyicilerin veya Razor Page modellerindeki özelliklerin değerleri [ViewDataDictionary'den](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)depolanır ve yüklenir.

Aşağıdaki örnekte, `AboutModel` `[ViewData]`'ile `Title` işaretlenmiş bir özellik içerir. Özellik, `Title` Hakkında sayfanın başlığına ayarlanır:

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

Hakkında sayfasında, `Title` özellik bir model özelliği olarak erişin:

```cshtml
<h1>@Model.Title</h1>
```

Düzende, başlık ViewData sözlüğünden okunur:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>Geçici Veriler

ASP.NET Core, [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller)ortaya çıkarır. Bu özellik, verileri okunana kadar depolar. Ve `Keep` `Peek` yöntemleri silme olmadan verileri incelemek için kullanılabilir. `TempData`tek bir istekten daha fazlası için veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod `Message` kullanma `TempData`değerini ayarlar:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Sayfalar/Müşteriler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirme, `Message` kullanmanın `TempData`değerini görüntüler.

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Sayfalar/Müşteriler/Index.cshtml.cs* sayfa modeli `[TempData]` `Message` özelliğiöznitelik uygular.

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için [TempData'ya](xref:fundamentals/app-state#tempdata) bakın.

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, `asp-page-handler` Etiket Yardımcısı'nı kullanarak iki işleyici için biçimlendirme oluşturur:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Önceki örnekteki formun her biri farklı bir `FormActionTagHelper` URL'ye göndermek için kullanılan iki gönderme düğmesi vardır. Öznitelik `asp-page-handler` bir `asp-page`arkadaşıdır. `asp-page-handler`bir sayfa tarafından tanımlanan işleyici yöntemlerinin her birine gönderen URL'ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığı için belirtilmemiştir.

Sayfa modeli:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Önceki kod *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, (varsa) sonra `On<HTTP Verb>` ve `Async` öncesinde ki addaki metin alınarak oluşturulur. Önceki örnekte, sayfa yöntemleri OnPost**JoinList**Async ve OnPost**JoinListUC**Async'dir. *OnPost* ve *Async* kaldırıldı, işleyici `JoinList` `JoinListUC`adları ve .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH?handler=JoinList` Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Özel yollar

Yönergeyi `@page` şu şekilde kullanın:

* Sayfaya özel bir rota belirtin. Örneğin, Hakkında sayfasına `/Some/Other/Path` giden yol . `@page "/Some/Other/Path"`
* Bir sayfanın varsayılan rotasına segmentleri ekleyin. Örneğin, bir "öğe" kesimi sayfanın varsayılan rotasına `@page "item"`.
* Parametreleri sayfanın varsayılan rotasına ekleyin. Örneğin, bir kimlik parametresi, `id`, . `@page "{id}"`

Yolun başında ki bir tilde`~`( ) tarafından belirlenen kök-göreli yol desteklenir. Örneğin, `@page "~/Some/Other/Path"` aynı `@page "/Some/Other/Path"`.

URL'deki sorgu dizesini `?handler=JoinList` beğenmezseniz, URL'nin yol bölümüne işleyici adını koymak için rotayı değiştirin. Rota, `@page` yönergeden sonra çift tırnak içinde bir rota şablonu eklenerek özelleştirilebilir.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, gönderen `OnPostJoinListAsync` URL yolu . `https://localhost:5001/Customers/CreateFATH/JoinList` Gönderen `OnPostJoinListUCAsync` URL yolu `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

`?` Aşağıdaki, `handler` rota parametresi isteğe bağlı olduğu anlamına gelir.

## <a name="configuration-and-settings"></a>Yapılandırma ve ayarlar

Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC oluşturucuüzerinde uzantı yöntemini kullanın:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Şu anda `RazorPagesOptions` sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz. Gelecekte daha fazla genişletilebilirlik sağlayacağız.

Görünümleri önceden derlemek [için, Bkz. Razor görünüm](xref:mvc/views/view-compilation) derlemesi.

[Örnek kodu indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample)

Bkz. Bu giriş üzerine inşa edilen [Razor Pages ile başlayın.](xref:tutorials/razor-pages/razor-pages-start)

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Razor Sayfalarının içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor Pages */Pages* dizininde köklenir. Razor Sayfalarınızın uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) [(ContentRootPath)](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)olduğunu belirtmek için [AddMvc'ye](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) ekleyin:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Razor Sayfalarının özel bir kök dizininde olduğunu belirtin

Razor Sayfalarınızın uygulamada özel bir kök dizininde olduğunu belirtmek için [AddMvc'ye](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin (göreceli bir yol sağlayın):

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
