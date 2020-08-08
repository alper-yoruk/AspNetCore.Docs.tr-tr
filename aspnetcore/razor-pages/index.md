---
title: RazorASP.NET Core sayfalara giriş
author: Rick-Anderson
description: RazorASP.NET Core sayfalarındaki sayfaların, MVC kullanmaktan daha kolay ve daha üretken bir şekilde kodlamasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 203d77a2caaba9156419a04754fe162dd57052c1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012715"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>RazorASP.NET Core sayfalara giriş

::: moniker range=">= aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)

RazorSayfalar, denetleyici ve görünümleri kullanmaktan daha kolay ve daha üretken bir şekilde kodlama sayfasına odaklanmış senaryolar yapabilir.

Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).

Bu belge, sayfalara giriş sağlar Razor . Adım adım öğretici değildir. Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start). ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>RazorSayfalar projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`dotnet new webapp`Komut satırından çalıştırın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .

---

## <a name="no-locrazor-pages"></a>RazorSayfaları

Razor*Startup.cs*'de sayfalar etkin:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür. Bu, farklı kılan [`@page`](xref:mvc/views/razor#page) yönergedir. `@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`sayfadaki ilk yönerge olmalıdır Razor . `@page`diğer yapıların davranışını etkiler [Razor](xref:mvc/views/razor) . RazorSayfaların dosya adlarında *. cshtml* soneki vardır.

Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir. *Pages/Index2. cshtml* dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2. cshtml. cs* sayfa modeli:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir. Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.

URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yolu               | eşleşen URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` veya `/Index` |
| */Pages/Contact.exe* | `/Contact` |
| */Pages/Store/Contact.exe* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.
* `Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.

## <a name="write-a-basic-form"></a>Temel form yazma

RazorSayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır. [Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor . Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :

Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) dosyasında başlatılır.

Bellek içi veritabanı `Microsoft.EntityFrameworkCore.InMemory` NuGet paketini gerektirir.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Veri modeli:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

DB bağlamı:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

*Pages/Create. cshtml. cs* sayfa modeli:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.

`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar. Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar. Bu ayrım şunları sağlar:

* [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.
* [Birim testi](xref:test/razor-pages-tests)

Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici metotları eklenebilir. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. Yukarıdaki kodda, `OnGet` yöntemi *CreateModel. cshtml* Razor sayfasını görüntüler.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:

* `OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.
* [Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu denetleyiciler ve sayfalarla aynı şekilde çalışır Razor . 

Önceki `OnPostAsync` Yöntem:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel akışı `OnPostAsync` :

Doğrulama hatalarını kontrol edin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Sayfalardan işlenmiş HTML */Create. cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Önceki kodda, formu deftere nakletme:

* Geçerli verilerle:

  * `OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> yardımcı yöntemini çağırır. `RedirectToPage`, bir <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> örneği döndürür. `RedirectToPage`:

    * Bir eylem sonucudur.
    * , Veya ile `RedirectToAction` benzerdir `RedirectToRoute` (denetleyiciler ve görünümlerde kullanılır).
    * Sayfalar için özelleştirilir. Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` . `RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

* Sunucuya geçirilen doğrulama hatalarıyla birlikte:

  * `OnPostAsync`Handler yöntemi <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> yardımcı yöntemini çağırır. `Page`, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> örneği döndürür. Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` . `PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür. Döndüren bir işleyici yöntemi `void` sayfayı işler.
  * Yukarıdaki örnekte, formun hiçbir değer olmadan nakledilmesi [ModelState ile sonuçlanır. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) yanlış döndürüyor. Bu örnekte, istemcide hiçbir doğrulama hatası gösterilmezler. Doğrulama hatası teslim etme bu belgenin ilerleyen bölümlerinde ele alınmıştır.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* İstemci tarafı doğrulaması tarafından algılanan doğrulama hatalarıyla birlikte:

  * Veriler sunucuya **nakledilmedi.**
  * İstemci tarafı doğrulaması bu belgenin ilerleyen kısımlarında açıklanmıştır.

`Customer`Özelliği, [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) model bağlamasını kabul etmek için özniteliğini kullanır:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`istemci tarafından değiştirilmemesi gereken özellikler içeren **modellerde kullanılmamalıdır.** Daha fazla bilgi için bkz. fazla [nakil](xref:data/ef-rp/crud#overposting).

RazorSayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` . Özelliklere bağlama, HTTP verilerini model türüne dönüştürmek için kod yazma ihtiyacını ortadan kaldırır. Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

*Sayfalar/oluşturma. cshtml* görünüm dosyası gözden geçiriliyor:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Yukarıdaki kodda, [giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` HTML `<input>` öğesini `Customer.Name` model ifadesine bağlar.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)Etiket Yardımcıları kullanılabilir hale getirir.

### <a name="the-home-page"></a>Giriş sayfası

*Index. cshtml* giriş sayfasıdır:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

*Index. cshtml* dosyası aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı. Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor .

*Index. cshtml* dosyası her müşteri için bir silme düğmesi oluşturmak için biçimlendirme içerir:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

İşlenmiş HTML:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Sil düğmesi HTML 'de işlendiğinde, bu nesnenin [biçimlendirme](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametreleri içerir:

* Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .
* , `handler` Özniteliği tarafından belirtilen `asp-page-handler` .

Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir. Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .

`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` . , Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

`OnPostDeleteAsync`Yöntemi:

* `id`Sorgu dizesinden alır.
* Müşteri iletişim için veritabanını sorgular `FindAsync` .
* Müşteri ilgili kişisi bulunursa, kaldırılır ve veritabanı güncelleştirilir.
* <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .

### <a name="the-editcshtml-file"></a>Edit. cshtml dosyası

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

İlk satır `@page "{id:int}"` yönergesini içerir. Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` . Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür. KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:

 ```cshtml
@page "{id:int?}"
```

*Edit.cshtml.cs* dosyası:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Doğrulama

Doğrulama kuralları:

* Model sınıfında bildirimli olarak belirtilir.
* Uygulamada her yerde zorlanır.

<xref:System.ComponentModel.DataAnnotations>Ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar. Dataaçıklamalarda, [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.

Modeli göz önünde bulundurun `Customer` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Aşağıdaki *Create. cshtml* görünüm dosyasını kullanarak:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Yukarıdaki kod:

* JQuery ve jQuery doğrulama betikleri içerir.
* `<div />`' İ `<span />` etkinleştirmek için ve [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) kullanır:

  * İstemci tarafı doğrulama.
  * Doğrulama hatası işleme.

* Aşağıdaki HTML 'yi oluşturur:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Create formunu ad değeri olmadan göndermek "ad alanı gereklidir" hata iletisini görüntüler. formunda. İstemcide JavaScript etkinse tarayıcı, sunucuya göndermeden hatayı görüntüler.

`[StringLength(10)]`Özniteliği `data-val-length-max="10"` işlenmiş html üzerinde oluşturulur. `data-val-length-max`tarayıcıların belirtilen uzunluk üst sınırından fazlasını girmesini engeller. Gönderiyi düzenlemek ve yeniden oynatmak için [Fiddler](https://www.telerik.com/fiddler) gibi bir araç kullanılıyorsa:

* , Adı 10 ' dan daha uzun.
* "Alan adı, en fazla 10 uzunluğunda bir dize olmalıdır" hata iletisi. hatası döndürülür.

Aşağıdaki modeli göz önünde bulundurun `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:

* `Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini gösterir, ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.
* `RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır. Yukarıdaki kodda, "tarz":

  * Yalnızca harfler kullanılmalıdır.
  * İlk harfin büyük harfle olması gerekir. Boşluk, sayı ve özel karakterlere izin verilmez.

* `RegularExpression`"Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki boşlukların içindeki özel karakter ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* `StringLength`Özniteliği bir dize özelliğinin uzunluk üst sınırını ve isteğe bağlı olarak en düşük uzunluğunu ayarlar.
* Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .

Model için Oluştur sayfasında, `Movie` geçersiz değerlere sahip hatalar görüntülenir:

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/razor-pages/validation/_static/val.png)

Daha fazla bilgi için bkz.

* [Film uygulamasına doğrulama ekleme](xref:tutorials/razor-pages/validation)
* [ASP.NET Core 'de model doğrulaması](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri

`HEAD`istekler belirli bir kaynak için üstbilgileri almaya izin verir. `GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.

Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

RazorBir `OnGet` işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` .

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF ve Razor Sayfalar

RazorSayfalar, [Antiforgery doğrulaması](xref:security/anti-request-forgery)tarafından korunur. [Formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) , antiforgery belirteçlerini HTML form öğelerine çıkartır.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor

Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor . Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*ve *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .

Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.

*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).
* JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.
* RazorSayfanın içeriği `@RenderBody()` olarak işlenir.

Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout).

[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/paylaşılan* klasöründedir. Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar. *Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.

Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.

Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** . *Görünümler/paylaşılan* bir MVC görünümleri modelidir. RazorSayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.

Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir. MVC denetleyicileri ve geleneksel görünümler ile kullanılan düzenler, şablonlar ve partilar Razor *yalnızca çalışır*.

Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır. `@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.

<a name="namespace"></a>

`@namespace`Bir sayfada ayarlanan yönerge:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

`@namespace`Yönergesi sayfanın ad alanını ayarlar. `@model`Yönergesinin ad alanını içermesi gerekmez.

`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` . Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.

Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.

`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*

*Pages/Create. cshtml* görünüm dosyasını göz önünde bulundurun:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Güncelleştirilmiş *sayfalar/oluşturma. cshtml* görünüm dosyası *_ViewImports. cshtml* ve önceki düzen dosyası:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Yukarıdaki kodda *_ViewImports. cshtml* ad alanı ve etiket yardımcıları içeri aktardı. Düzen dosyası JavaScript dosyalarını içeri aktardı.

[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.

Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Pages*

  * *Index.cshtml*
  * *Gizlilik. cshtml*
  * */Customers*

    * *. Cshtml oluştur*
    * *Edit. cshtml*
    * *Index.cshtml*

*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *sayfaları/müşterileri/Index. cshtml* 'ye yeniden yönlendirir. Dize, `./Index` önceki sayfaya erişmek için kullanılan göreli bir sayfa adıdır. *Pages/Customers/Index. cshtml* sayfasının URL 'leri oluşturmak için kullanılır. Örnek:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Mutlak sayfa adı, `/Index` *Sayfalar/Index. cshtml* sayfasına URL 'ler oluşturmak için kullanılır. Örnek:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` . Önceki URL oluşturma örnekleri, bir URL 'YI sabit kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, `RedirectToPage` *sayfalarda/müşteriler/Create. cshtml*'de farklı parametreler kullanılarak hangi dizin sayfasının seçildiği gösterilmektedir.

| RedirectToPage (x)| Sayfa |
| ----------------- | ------------ |
| RedirectToPage ("/Index") | *Sayfa/dizin* |
| RedirectToPage ("./Index"); | *Sayfalar/müşteriler/Dizin* |
| RedirectToPage (".. /İndex ") | *Sayfa/dizin* |
| RedirectToPage ("Dizin")  | *Sayfalar/müşteriler/Dizin* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*. `RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .

Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur. Bir klasördeki sayfalar arasında bağlantı için göreli adlar kullanıldığında:

* Bir klasörü yeniden adlandırmak, göreli bağlantıları bozmaz.
* Klasör adını içermediği için bağlantılar kopuk değildir.

Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için <xref:mvc/controllers/areas> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veri, ile bir sayfaya geçirilebilir <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> . Özniteliği olan Özellikler `[ViewData]` , değerlerinin depolandığı ve öğesinden yüklendiği değerlerdir <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .

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

ASP.NET Core, öğesini kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Bu özellik, okunana kadar verileri depolar. <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> yöntemleri silmeden verileri incelemek için kullanılabilir. `TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır. `asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir. `asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.

Sayfa modeli:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` . Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir. *Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . ' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Özel yollar

`@page`İçin yönergesini kullanın:

* Sayfaya özel bir yol belirtin. Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .
* Kesimleri bir sayfanın varsayılan yoluna ekleyin. Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .
* Bir sayfanın varsayılan yoluna parametreleri ekleyin. Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .

Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir. Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .

URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin. Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` . ' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.

## <a name="advanced-configuration-and-settings"></a>Gelişmiş yapılandırma ve ayarlar

Aşağıdaki bölümlerdeki yapılandırma ve ayarlar çoğu uygulama için gerekli değildir.

Gelişmiş seçenekleri yapılandırmak için, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> yapılandıran aşırı yüklemeyi kullanın <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Sayfalar için <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> kök dizini ayarlamak üzere öğesini kullanın veya sayfalar için uygulama modeli kuralları ekleyin. Kurallar hakkında daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization).

Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>RazorSayfaların içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır. <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> Razor Sayfalarınızın, uygulamanın [içerik kökünde](xref:fundamentals/index#content-root) () olduğunu belirtmek için ekleyin <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> :

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>RazorSayfaların özel kök dizinde olduğunu belirtme

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>Bu Razor sayfaların, uygulamadaki özel bir kök dizinde olduğunu belirtmek için ekleyin (göreli bir yol sağlayın):

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).
* [Özniteliği ve Razor sayfaları yetkilendir](xref:security/authorization/simple#aarp)
* [Örnek kodu indirme veya görüntüleme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Ryan şimdi ak](https://github.com/rynowak)

RazorSayfalar, kod odaklı senaryoları daha kolay ve daha üretken hale getiren ASP.NET Core MVC 'nin yeni bir yönüdür.

Model-View-Controller yaklaşımını kullanan bir öğretici arıyorsanız, bkz. [ASP.NET Core MVC ile çalışmaya başlama](xref:tutorials/first-mvc-app/start-mvc).

Bu belge, sayfalara giriş sağlar Razor . Adım adım öğretici değildir. Bölümlerden bazılarını çok gelişmiş bir şekilde buldıysanız, bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start). ASP.NET Core genel bir bakış için bkz. [ASP.NET Core giriş](xref:index).

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>RazorSayfalar projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bir sayfa projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) Razor .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

`dotnet new webapp`Komut satırından çalıştırın.

Oluşturulan *. csproj* dosyasını Mac için Visual Studio açın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`dotnet new webapp`Komut satırından çalıştırın.

---

## <a name="no-locrazor-pages"></a>RazorSayfaları

Razor*Startup.cs*'de sayfalar etkin:

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Temel bir sayfa düşünün:<a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Yukarıdaki kod, denetleyiciler ve görünümlerle ASP.NET Core bir uygulamada kullanılan bir [ Razor görünüm dosyası](xref:tutorials/first-mvc-app/adding-view) gibi çok sayıda görünür. Bu, farklı kılan `@page` yönergedir. `@page`dosyayı bir MVC eylemine dönüştürür. Bu, bir denetleyiciden geçmeden istekleri doğrudan işlediği anlamına gelir. `@page`sayfadaki ilk yönerge olmalıdır Razor . `@page`diğer yapıların davranışını etkiler Razor .

Bir sınıf kullanan benzer bir sayfa `PageModel` aşağıdaki iki dosyada gösterilmiştir. *Pages/Index2. cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

*Pages/Index2. cshtml. cs* sayfa modeli:

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Kural gereği, `PageModel` sınıf dosyası Razor *. cs* eklenmiş olan sayfa dosyasıyla aynı ada sahiptir. Örneğin, önceki Razor sayfa *Pages/Index2. cshtml*' dir. Sınıfını içeren dosya `PageModel` *sayfa/Index2. cshtml. cs*olarak adlandırılır.

URL yollarının sayfalara olan ilişkilendirmeleri, sayfanın dosya sistemindeki konumuna göre belirlenir. Aşağıdaki tabloda, bir Razor sayfa yolu ve eşleşen URL gösterilmektedir:

| Dosya adı ve yolu               | eşleşen URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` veya `/Index` |
| */Pages/Contact.exe* | `/Contact` |
| */Pages/Store/Contact.exe* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` veya `/Store/Index` |

Notlar:

* Çalışma zamanı sayfalar Razor klasöründeki sayfalar dosyalarını varsayılan olarak *Pages* arar.
* `Index`, URL bir sayfa içermiyorsa varsayılan sayfasıdır.

## <a name="write-a-basic-form"></a>Temel form yazma

RazorSayfalar, Web tarayıcıları ile kullanılan yaygın desenleri bir uygulama oluştururken kolayca uygulanması için tasarlanmıştır. [Model bağlama](xref:mvc/models/model-binding), [ETIKET yardımcıları](xref:mvc/views/tag-helpers/intro)ve HTML Yardımcıları hepsi yalnızca bir sayfa sınıfında tanımlanan özelliklerle *çalışır* Razor . Model için temel bir "bize başvurun" formu uygulayan bir sayfa düşünün `Contact` :

Bu belgedeki örnekler için, `DbContext` [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) dosyasında başlatılır.

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Veri modeli:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

DB bağlamı:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

*Pages/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

*Pages/Create. cshtml. cs* sayfa modeli:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Kuralına göre, `PageModel` sınıfı çağrılır `<PageName>Model` ve sayfayla aynı ad alanında bulunur.

`PageModel`Sınıfı, bir sayfanın mantığının sunumuna ayrılmasını sağlar. Sayfaya gönderilen istekler için sayfa işleyicilerini ve sayfayı işlemek için kullanılan verileri tanımlar. Bu ayrım şunları sağlar:

* [Bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla sayfa bağımlılıklarını yönetme.
* Sayfaların [birim testi](xref:test/razor-pages-tests) .

Sayfada `OnPostAsync` , istekler üzerinde çalışan bir *işleyici yöntemi*vardır `POST` (bir Kullanıcı formu gönderdiğinde). Herhangi bir HTTP fiili için işleyici yöntemleri ekleyebilirsiniz. En yaygın işleyiciler şunlardır:

* Sayfa için gereken durumu başlatmak için `OnGet`. [OnGet](#OnGet) örneği.
* Form gönderilerini işlemek için `OnPost`.

`Async` adlandırma son eki isteğe bağlıdır, ancak genellikle zaman uyumsuz işlevler için kural tarafından kullanılır. Yukarıdaki kod, sayfalar için tipik bir davranıştır Razor .

Denetleyicileri ve görünümleri kullanarak ASP.NET uygulamaları hakkında bilginiz varsa:

* `OnPostAsync`Yukarıdaki örnekteki kod, tipik denetleyici koduna benzer şekilde görünür.
* [Model bağlama](xref:mvc/models/model-binding), [doğrulama](xref:mvc/models/validation), [doğrulama](xref:mvc/models/validation)ve eylem sonuçları gibi mvc temel elemanlarının çoğu paylaşılır.

Önceki `OnPostAsync` Yöntem:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Temel akışı `OnPostAsync` :

Doğrulama hatalarını kontrol edin.

* Hata yoksa, verileri kaydedin ve yeniden yönlendirin.
* Hatalar varsa, doğrulama iletileriyle sayfayı yeniden görüntüleyin. İstemci tarafı doğrulaması geleneksel ASP.NET Core MVC uygulamalarıyla aynıdır. Çoğu durumda, doğrulama hataları istemci üzerinde algılanır ve sunucuya hiçbir zaman gönderilmez.

Veriler başarıyla girildiğinde, `OnPostAsync` işleyici yöntemi `RedirectToPage` bir örneğini döndürmek için yardımcı yöntemini çağırır `RedirectToPageResult` . `RedirectToPage`, `RedirectToAction` veya ' a benzer ancak sayfalara özelleştirilmiş yeni bir eylem sonucudur `RedirectToRoute` . Yukarıdaki örnekte, kök dizin sayfasına () yeniden yönlendiriliyor `/Index` . `RedirectToPage`, [Sayfalar Için URL oluşturma](#url_gen) bölümünde ayrıntılı olarak açıklanmıştır.

Gönderilen formda doğrulama hataları olduğunda (sunucuya geçirilen), `OnPostAsync` işleyici yöntemi `Page` yardımcı yöntemini çağırır. `Page`, bir `PageResult` örneği döndürür. Döndürme `Page` , denetleyicilerde eylemlerin nasıl dönüşlerine benzer `View` . `PageResult`, bir işleyici yöntemi için varsayılan dönüş türüdür. Döndüren bir işleyici yöntemi `void` sayfayı işler.

`Customer`Özelliği `[BindProperty]` model bağlamayı kabul etmek için özniteliğini kullanır.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

RazorSayfalar, varsayılan olarak, özellikleri yalnızca fiil dışı özelliklerle bağlayın `GET` . Özelliklere bağlamak, yazmanız gereken kod miktarını azaltabilir. Bağlama, form alanlarını işlemek için aynı özelliği kullanarak kodu azaltır ( `<input asp-for="Customer.Name">` ) ve girişi kabul eder.

[!INCLUDE[](~/includes/bind-get.md)]

Giriş sayfası (*Index. cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

İlişkili `PageModel` Sınıf (*Index.cshtml.cs*):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

*Index. cshtml* dosyası her kişi için bir düzenleme bağlantısı oluşturmak üzere aşağıdaki biçimlendirmeyi içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Tutturucu etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) , `asp-route-{value}` düzenleme sayfasına bir bağlantı oluşturmak için özniteliğini kullandı. Bağlantı, iletişim KIMLIĞINE sahip rota verileri içerir. Örneğin, `https://localhost:5001/Edit/1`. [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) , sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor . Etiket Yardımcıları tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

*Pages/Edit. cshtml* dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

İlk satır `@page "{id:int}"` yönergesini içerir. Yönlendirme kısıtlaması, `"{id:int}"` sayfada yönlendirme verileri içeren sayfaya istekleri kabul etmesini söyler `int` . Sayfaya yapılan bir istek öğesine dönüştürülebileceği rota verileri içermiyorsa `int` , çalışma zamanı BIR HTTP 404 (bulunamadı) hatası döndürür. KIMLIĞI isteğe bağlı yapmak için `?` yol kısıtlamasına ekleyin:

 ```cshtml
@page "{id:int?}"
```

*Pages/Edit. cshtml. cs* dosyası:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

*Index. cshtml* dosyası, her müşteri kişisi için bir silme düğmesi oluşturmak için de biçimlendirme içerir:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Sil düğmesi HTML biçiminde işlendiğinde, `formaction` için parametreleri içerir:

* Özniteliği tarafından belirtilen müşteri iletişim KIMLIĞI `asp-route-id` .
* `handler`Özniteliği tarafından belirtilen `asp-page-handler` .

Aşağıda, müşteri irtibat KIMLIĞIYLE birlikte işlenmiş bir Delete düğmesine örnek verilmiştir `1` :

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Düğme seçildiğinde, sunucuya bir form `POST` isteği gönderilir. Kurala göre, işleyici yönteminin adı, `handler` şemaya göre parametrenin değerine göre seçilir `OnPost[handler]Async` .

`handler` `delete` Bu örnekte olduğundan, `OnPostDeleteAsync` isteği işlemek için işleyici yöntemi kullanılır `POST` . , Gibi `asp-page-handler` farklı bir değere ayarlandıysa, `remove` adında bir işleyici yöntemi `OnPostRemoveAsync` seçilir. Aşağıdaki kod `OnPostDeleteAsync` işleyiciyi göstermektedir:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

`OnPostDeleteAsync`Yöntemi:

* Sorgu dizesinden öğesini kabul eder `id` . *Index. cshtml* sayfa yönergesi yönlendirme kısıtlaması içeriyorsa `"{id:int?}"` , `id` Rota verilerinden gelir. İçin rota verileri, gibi `id` URI 'de belirtilmiştir `https://localhost:5001/Customers/2` .
* Müşteri iletişim için veritabanını sorgular `FindAsync` .
* Müşteri ilgili kişisi bulunursa, bunlar müşteri kişileri listesinden kaldırılır. Veritabanı güncelleştirildi.
* `RedirectToPage`Kök dizin sayfasına () yeniden yönlendirmek için çağrılar `/Index` .

## <a name="mark-page-properties-as-required"></a>Sayfa özelliklerini gerektiği gibi işaretle

İçindeki Özellikler `PageModel` [gerekli](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) öznitelikle işaretlenebilir:

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Daha fazla bilgi için bkz. [model doğrulaması](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>OnGet işleyicisi geri dönüşü ile tanıtıcı HEAD istekleri

`HEAD`istekleri belirli bir kaynak için üstbilgileri almanıza izin verir. `GET`İsteklerin aksine `HEAD` istekler bir yanıt gövdesi döndürmez.

Normalde, `OnHead` istekler için bir işleyici oluşturulur ve çağırılır `HEAD` : 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

ASP.NET Core 2,1 veya üzeri sürümlerde, Razor `OnGet` hiçbir işleyici tanımlanmazsa, sayfa işleyiciyi çağırmaya geri döner `OnHead` . Bu davranış, içindeki [Setcompatibilityversion](xref:mvc/compatibility-version) çağrısıyla etkinleştirilir `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Varsayılan Şablonlar `SetCompatibilityVersion` ASP.NET Core 2,1 ve 2,2 ' de çağrıyı oluşturur. `SetCompatibilityVersion`Razorsayfa seçeneğini etkin bir `AllowMappingHeadRequestsToGetHandler` şekilde ayarlar `true` .

İle tüm davranışlardan çıkmak yerine `SetCompatibilityVersion` , açıkça *belirli* davranışları kabul edebilirsiniz. Aşağıdaki kod, isteklerin işleyiciye eşlenmesine izin vermek için ' de `HEAD` kullanılır `OnGet` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF ve Razor Sayfalar

[Antiforgery doğrulaması](xref:security/anti-request-forgery)için herhangi bir kod yazmanız gerekmez. Antiforgery belirteci oluşturma ve doğrulama, sayfalara otomatik olarak eklenir Razor .

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Sayfalarla düzenleri, partileri, şablonları ve etiket yardımcıları kullanma Razor

Sayfalar, görünüm altyapısının tüm özellikleri ile çalışır Razor . Düzenler, partıals, şablonlar, etiket yardımcıları, *_ViewStart. cshtml*, *_ViewImports. cshtml* geleneksel görünümlerde aynı şekilde çalışır Razor .

Bu özelliklerden bazılarının avantajlarından yararlanarak bu sayfayı declutter edelim.

*Sayfa/paylaşılan/_Layout. cshtml*'ye bir [Düzen sayfası](xref:mvc/views/layout) ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

[Düzen](xref:mvc/views/layout):

* Her sayfanın yerleşimini denetler (sayfa düzen dışında değilse).
* JavaScript ve stil sayfaları gibi HTML yapılarını içeri aktarır.

Daha fazla bilgi için bkz. [Düzen sayfası](xref:mvc/views/layout) .

[Layout](xref:mvc/views/layout#specifying-a-layout) özelliği *Pages/_ViewStart. cshtml*' de ayarlanır:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Düzen *Sayfalar/paylaşılan* klasöründedir. Sayfalar, geçerli sayfayla aynı klasörden başlayarak diğer görünümleri (düzenler, şablonlar, parals) hiyerarşik olarak arar. *Sayfalar/paylaşılan* klasördeki bir düzen, Razor *Sayfalar* klasörü altındaki herhangi bir sayfadan kullanılabilir.

Düzen dosyası *Sayfalar/paylaşılan* klasörüne gitmelidir.

Düzen dosyasını *Görünümler/paylaşılan* klasöre **yerleştirmenizi öneririz** . *Görünümler/paylaşılan* bir MVC görünümleri modelidir. RazorSayfalar, yol kurallarını değil klasör hiyerarşisine güvenmektir.

Bir sayfadan aramayı görüntüleme Razor *sayfaları* klasörünü içerir. MVC denetleyicileri ve geleneksel görünümler ile kullanmakta olduğunuz düzenler, şablonlar ve parals Razor *yalnızca çalışır*.

Bir *Pages/_ViewImports. cshtml* dosyası ekleyin:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace`, Öğreticinin ilerleyen kısımlarında açıklanmıştır. `@addTagHelper`Yönergesi, [yerleşik etiket yardımcılarını](xref:mvc/views/tag-helpers/builtin-th/Index) *Sayfalar* klasöründeki tüm sayfalara getirir.

<a name="namespace"></a>

`@namespace`Yönerge bir sayfada açıkça kullanıldığında:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Yönergesi sayfanın ad alanını ayarlar. `@model`Yönergesinin ad alanını içermesi gerekmez.

`@namespace`Yönerge *_ViewImports. cshtml*içinde yer aldığında, belirtilen ad alanı, yönergeyi içeri aktaran sayfada oluşturulan ad alanı için ön ek sağlar `@namespace` . Oluşturulan ad alanı (sonek bölümü) geri kalanı, *_ViewImports. cshtml* içeren klasör ve sayfayı içeren klasör arasındaki noktayla ayrılmış göreli yoldur.

Örneğin, `PageModel` *Pages/Customers/Edit. cshtml. cs* sınıfı, ad alanını açıkça ayarlar:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

*Pages/_ViewImports. cshtml* dosyası aşağıdaki ad alanını ayarlar:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

*Pages/Customers/Edit. cshtml* sayfası için oluşturulan ad alanı, Razor `PageModel` sınıfıyla aynıdır.

`@namespace`*Ayrıca geleneksel görünümlerle de geçerlidir Razor .*

Özgün *Sayfalar/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Güncelleştirilmiş *Sayfalar/Create. cshtml* görünüm dosyası:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

[ Razor Başlangıç projesi sayfaları](#rpvs17) */_ValidationScriptsPartial. cshtml*, Istemci tarafı doğrulama 'yı bağlayan sayfaları içerir.

Kısmi görünümler hakkında daha fazla bilgi için bkz <xref:mvc/views/partial> ..

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Sayfalar için URL oluşturma

`Create`Daha önce gösterilen sayfa şunları kullanır `RedirectToPage` :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Uygulama aşağıdaki dosya/klasör yapısına sahiptir:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *. Cshtml oluştur*
    * *Edit. cshtml*
    * *Index.cshtml*

*Pages/Customers/Create. cshtml* ve *Pages/Customers/Edit. cshtml* sayfaları, başarılı olduktan sonra *Pages/Index. cshtml* dosyasına yönlendirilir. Dize, `/Index` önceki sayfaya erişmek IÇIN URI 'nin bir parçasıdır. Dize, `/Index` *Sayfalar/Index. cshtml* sayfasına URI 'ler oluşturmak için kullanılabilir. Örnek:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Sayfa adı, kök */Pages* klasöründeki sayfanın başında `/` (örneğin,) bir yoldur `/Index` . Önceki URL oluşturma örnekleri bir URL 'YI kodlamadan gelişmiş seçenekler ve işlevsel yetenekler sunar. URL oluşturma [yönlendirme](xref:mvc/controllers/routing) kullanır ve yolun hedef yolda nasıl tanımlandığınıza göre parametreleri oluşturabilir ve kodlayabilir.

Sayfalar için URL oluşturma göreli adları destekler. Aşağıdaki tabloda, `RedirectToPage` *sayfa/müşteri/oluşturma. cshtml*'den farklı parametrelerle hangi dizin sayfasının seçildiği gösterilmektedir:

| RedirectToPage (x)| Sayfa |
| ----------------- | ------------ |
| RedirectToPage ("/Index") | *Sayfa/dizin* |
| RedirectToPage ("./Index"); | *Sayfalar/müşteriler/Dizin* |
| RedirectToPage (".. /İndex ") | *Sayfa/dizin* |
| RedirectToPage ("Dizin")  | *Sayfalar/müşteriler/Dizin* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` ve, `RedirectToPage("../Index")` *göreli adlardır*. `RedirectToPage`Parametresi, hedef sayfanın adını hesaplamak için geçerli sayfanın yoluyla *birleştirilir* .  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Karmaşık bir yapıya sahip siteler oluştururken göreli ad bağlama yararlı olur. Bir klasördeki sayfalar arasında bağlantı sağlamak için göreli adlar kullanırsanız, bu klasörü yeniden adlandırabilirsiniz. Tüm bağlantılar hala çalışır (klasör adını içermediği için).

Farklı bir [alandaki](xref:mvc/controllers/areas)bir sayfaya yeniden yönlendirmek için alanını belirtin:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Daha fazla bilgi için bkz. <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>ViewData özniteliği

Veri, [Viewdataattribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)içeren bir sayfaya geçirilebilir. ' Deki denetleyicilerde veya Razor sayfa modellerinde bulunan özelliklerin `[ViewData]` değerleri, [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)'den depolanır ve yüklenir.

Aşağıdaki örnekte, `AboutModel` `Title` ile işaretlenmiş bir özelliği içerir `[ViewData]` . `Title`Özelliği hakkında sayfasının başlığına ayarlanır:

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

ASP.NET Core bir [denetleyicide](/dotnet/api/microsoft.aspnetcore.mvc.controller) [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) özelliğini kullanıma sunar. Bu özellik, okunana kadar verileri depolar. `Keep`Ve `Peek` yöntemleri silmeden verileri incelemek için kullanılabilir. `TempData`, bir tek istekten daha fazla veri gerektiğinde yeniden yönlendirme için yararlıdır.

Aşağıdaki kod, şunu kullanarak değerini ayarlar `Message` `TempData` :

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

*Pages/Customers/Index. cshtml* dosyasında aşağıdaki biçimlendirme, using değerini gösterir `Message` `TempData` .

```cshtml
<h3>Msg: @Model.Message</h3>
```

*Pages/Customers/Index. cshtml. cs* sayfa modeli, `[TempData]` özelliğine özniteliğini uygular `Message` .

```csharp
[TempData]
public string Message { get; set; }
```

Daha fazla bilgi için bkz. [TempData](xref:fundamentals/app-state#tempdata) .

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Sayfa başına birden çok işleyici

Aşağıdaki sayfa, etiket Yardımcısını kullanarak iki işleyici için biçimlendirme oluşturur `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Yukarıdaki örnekteki formda, her biri `FormActionTagHelper` farklı BIR URL 'ye göndermek için kullanan iki gönderme düğmesi vardır. `asp-page-handler`Özniteliği, için bir yardımcı ' `asp-page` dir. `asp-page-handler`bir sayfa tarafından tanımlanan her bir işleyici yöntemini gönderen URL 'Ler oluşturur. `asp-page`örnek geçerli sayfaya bağlandığından belirtilmedi.

Sayfa modeli:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Yukarıdaki kod, *adlandırılmış işleyici yöntemlerini*kullanır. Adlandırılmış işleyici yöntemleri, `On<HTTP Verb>` ve öncesinde (varsa) ad içindeki metin alınarak oluşturulur `Async` . Yukarıdaki örnekte, Page metotları OnPost**Joinlist**Async ve onpost**Joinlıstuc**Async ' dir. *Onpost* Ile *zaman uyumsuz* olarak kaldırıldığında, işleyici adları ve ' dir `JoinList` `JoinListUC` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` . ' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .

## <a name="custom-routes"></a>Özel yollar

`@page`İçin yönergesini kullanın:

* Sayfaya özel bir yol belirtin. Örneğin, hakkında sayfasına olan yol ile öğesine ayarlanabilir `/Some/Other/Path` `@page "/Some/Other/Path"` .
* Kesimleri bir sayfanın varsayılan yoluna ekleyin. Örneğin, bir "öğe" segmenti sayfanın varsayılan rotasına eklenebilir `@page "item"` .
* Bir sayfanın varsayılan yoluna parametreleri ekleyin. Örneğin, bir ID parametresi, `id` içeren bir sayfa için gerekli olabilir `@page "{id}"` .

Yolun başındaki bir tilde () tarafından belirlenen kök göreli bir yol `~` desteklenir. Örneğin, `@page "~/Some/Other/Path"` ile aynıdır `@page "/Some/Other/Path"` .

URL 'de sorgu dizesini beğenmezseniz `?handler=JoinList` , URL 'nin yol bölümüne işleyici adını koymak için yolu değiştirin. Yol, yönergeden sonra çift tırnak içine alınmış bir rota şablonu eklenerek özelleştirilebilir `@page` .

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Önceki kodu kullanarak, ' a gönderen URL yolu `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` . ' A gönderen URL yolu `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .

`?`Aşağıda `handler` yol parametresinin isteğe bağlı olduğu anlamına gelir.

## <a name="configuration-and-settings"></a>Yapılandırma ve ayarlar

Gelişmiş seçenekleri yapılandırmak için, `AddRazorPagesOptions` MVC Oluşturucu 'da genişletme yöntemini kullanın:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Şu anda `RazorPagesOptions` ' nı, sayfalar için kök dizini ayarlamak veya sayfalar için uygulama modeli kuralları eklemek için kullanabilirsiniz. Gelecekte bu şekilde daha fazla genişletilebilirlik etkinleştireceğiz.

Görünümleri önceden derlemek için bkz. [ Razor derlemeyi görüntüle](xref:mvc/views/view-compilation) .

[Örnek kodu indirin veya görüntüleyin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Bu giriş hakkında daha fazla bilgi için bkz. [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>RazorSayfaların içerik kökünde olduğunu belirtin

Varsayılan olarak, Razor Sayfalar, */Pages* dizininde kök olarak depolanır. Sayfalarınızın uygulamanın içerik kökünde (contentrootpath) olduğunu belirtmek için [ Razor pagesatcontentroot Ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) [addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) ' ye ekleyin Razor : [content root](xref:fundamentals/index#content-root) [ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>RazorSayfaların özel kök dizinde olduğunu belirtme

Sayfalarınızın uygulamadaki özel bir kök dizinde olduğunu belirtmek için [Addmvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 'ye [ Razor pagesroot ile](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) ekleyin Razor (göreli bir yol sağlayın):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Özniteliği ve Razor sayfaları yetkilendir](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
