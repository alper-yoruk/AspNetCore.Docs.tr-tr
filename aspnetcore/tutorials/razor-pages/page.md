---
title: ASP.NET Core İskeleli Jilet Sayfaları
author: rick-anderson
description: İskele tarafından oluşturulan Jilet Sayfaları açıklar.
ms.author: riande
ms.date: 08/17/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: cec4295a2c08c89db0975808583f41c7d09bfc88
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662451"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>ASP.NET Core İskeleli Jilet Sayfaları

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğretici, [önceki öğreticide](xref:tutorials/razor-pages/model)iskele tarafından oluşturulan Razor Pages inceler.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Sayfaları Oluştur, Sil, Ayrıntı ve Düzenle

*Sayfaları/Filmleri/Index.cshtml.cs* Sayfa Modelini Inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Jilet Sayfaları `PageModel`türetilmiştir. Kural olarak, `PageModel`-türetilmiş `<PageName>Model`sınıf denir. Oluşturucu sayfaya eklemek için `RazorPagesMovieContext` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) kullanır. Tüm iskele sayfaları bu deseni takip eder. Entity Framework ile asynchronous programlama hakkında daha fazla bilgi için [Asynchronous koduna](xref:data/ef-rp/intro#asynchronous-code) bakın.

Sayfa için bir istek yapıldığında, `OnGetAsync` yöntem bir film listesini Razor Page'e döndürür. `OnGetAsync`veya `OnGet` sayfanın durumunu başlatmaya çağrılır. Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.

İade `OnGet` `void` veya `OnGetAsync` `Task`rirken, iade deyimi kullanılmaz. İade türü `IActionResult` veya `Task<IActionResult>`, bir iade deyimi sağlanmalıdır. Örneğin, *Sayfalar/Filmler/Create.cshtml.cs* `OnPostAsync` yöntemi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>*Sayfaları/Filmleri/Index.cshtml* Jilet Sayfasını Inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor HTML'den C#'a veya Jilete özgü biçimlendirmeye geçiş yapabilir. Bir `@` [sembol, Jilet ayrılmış](xref:mvc/views/razor#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür, aksi takdirde C#'a dönüşür.

### <a name="the-page-directive"></a>Direktif @page

`@page` Razor yönergesi dosyayı bir MVC eylemi yapar, bu da istekleri işleyebilir anlamına gelir. `@page`bir sayfadaki ilk Razor direktifi olmalıdır. `@page`Jilete özgü biçimlendirmeye geçişin bir örneğidir. Daha fazla bilgi için [Razor sözdizimine](xref:mvc/views/razor#razor-syntax) bakın.

Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

HTML `DisplayNameFor` Yardımcısı, görüntü `Title` adını belirlemek için lambda ifadesinde başvurulan özelliği denetler. Lambda ifadesi değerlendirilmek yerine incelenir. Bu, `model`, , , `model.Movie`veya `model.Movie[0]` boş `null` olduğunda hiçbir erişim ihlali anlamına gelir. Lambda ifadesi değerlendirildiğinde (örneğin, modelin özellik değerleri `@Html.DisplayFor(modelItem => item.Title)`ile) değerlendirilir.

<a name="md"></a>

### <a name="the-model-directive"></a>Direktif @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Yönerge, `@model` Razor Page'e geçirilen modelin türünü belirtir. Önceki örnekte, `@model` satır `PageModel`-türetilmiş sınıfı Razor Page için kullanılabilir hale getirir. Model, sayfadaki `@Html.DisplayNameFor` HTML `@Html.DisplayFor` Yardımcıları ve [HTML Yardımcıları'nda](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) kullanılır.

### <a name="the-layout-page"></a>Düzen sayfası

Menü bağlantılarını seçin **(RazorPagesMovie,** **Home**, ve **Privacy**). Her sayfa aynı menü düzenini gösterir. Menü düzeni *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında uygulanır. *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları HTML kapsayıcı düzeninin aşağıdakileri olmasını sağlar:

* Tek bir yerde belirtilir.
* Sitede birden çok sayfada uygulanır.

`@RenderBody()` Hattı bul. `RenderBody`sayfaya özel tüm görünümlerin bulunduğu ve düzen sayfasına *sarılmış* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçin ve *Pages/Privacy.cshtml* görünümü `RenderBody` yöntemin içinde işlenir.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData ve düzen

*Sayfalar/Filmler/Index.cshtml* dosyasındaki aşağıdaki biçimlendirmeyi göz önünde bulundurun:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Önceki vurgulanan biçimlendirme, Razor'Un C#'a geçişine bir örnektir. `{` Ve `}` karakterler C# kodu bloğunu içine alar.

Taban `PageModel` sınıf, `ViewData` verileri Görünüm'e geçirmek için kullanılabilecek bir sözlük özelliği içerir. Nesneler anahtar/değer `ViewData` deseni kullanılarak sözlüklere eklenir. Önceki örnekte, `"Title"` özellik sözlük eklenir. `ViewData`

`"Title"` Özellik, *Pages/Shared/_Layout.cshtml* dosyasında kullanılır. Aşağıdaki biçimlendirme *_Layout.cshtml* dosyasının ilk birkaç satırını gösterir.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Satır `@*Markup removed for brevity.*@` bir Razor yorumdur. HTML yorumlarının`<!-- -->`aksine ( ), Razor yorumları istemciye gönderilmez.

### <a name="update-the-layout"></a>Düzeni güncelleştirme

`<title>` *Sayfa/Paylaşılan/_Layout.cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **Film** görüntülemek için değiştirin.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında aşağıdaki bağlantı öğesini bulun.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Önceki bağlantı öğesi bir [Etiket Yardımcısı'dır.](xref:mvc/views/tag-helpers/intro) Bu durumda, [Çapa Etiketi Yardımcısı.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-page="/Movies/Index"` Tag Helper özniteliği ve `/Movies/Index` değeri, Razor Page'e bir bağlantı oluşturur. Öznitelik `asp-area` değeri boşolduğundan, alan bağlantıda kullanılmaz. Daha fazla bilgi için [Alanlar'a](xref:mvc/controllers/areas) bakın.

Değişikliklerinizi kaydedin ve **RpMovie** bağlantısına tıklayarak uygulamayı test edin. Herhangi bir sorun varsa GitHub [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.

Diğer bağlantıları test edin **(Ana Sayfa**, **RpMovie**, **Oluştur**, **Düzenle**ve **Sil**). Her sayfa, tarayıcı sekmesinde görebileceğiniz başlığı ayarlar. Bir sayfayı yer imi yaptığınızda, başlık yer imi için kullanılır.

> [!NOTE]
> `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halklar ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için, uygulamanızı küreselleştirmek için adımlar atmalısınız. Ondalık virgül ekleme yönergeleri için bu [GitHub sorunu 4076'ya](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) bakın.

Özellik `Layout` *Pages/_ViewStart.cshtml* dosyasında ayarlanır:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Önceki biçimlendirme, düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/Paylaşılan/_Layout.cshtml* olarak ayarlar. Daha fazla bilgi için [Düzen'e](xref:razor-pages/index#layout) bakın.

### <a name="the-create-page-model"></a>Sayfa oluştur modeli

*Sayfalar/Filmler/Create.cshtml.cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Yöntem, `OnGet` sayfa için gereken durumu başlangıç olarak belirtir. Create sayfasının başlatılması için herhangi bir durumu `Page` yoktur, bu nedenle döndürülür. Daha sonra öğreticide, `OnGet` durum başlatma örneği gösterilir. Yöntem, `Page` `PageResult` *Create.cshtml* sayfasını işleyen bir nesne oluşturur.

`Movie` Özellik, `[BindProperty]` [bağlamayı modellemek](xref:mvc/models/model-binding)için özniteliği kullanır. Form oluştur form değerlerini yayınladığında, ASP.NET Çekirdek çalışma süresi deftere `Movie` nakledilen değerleri modele bağlar.

Yöntem, `OnPostAsync` sayfa gönderileri veri oluşturduğunda çalıştırılır:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Herhangi bir model hatası varsa, form, deftere nakledilen form verileriyle birlikte yeniden görüntülenir. Çoğu model hatası form deftere nakledilmeden önce istemci tarafında yakalanabilir. Model hatasıörneği, tarih alanı için tarihe dönüştürülemeyen bir değer deftere nakletmektir. İstemci tarafı doğrulama ve model doğrulama daha sonra öğreticide ele alınmıştır.

Model hatası yoksa, veriler kaydedilir ve tarayıcı Dizin sayfasına yönlendirilir.

### <a name="the-create-razor-page"></a>Jilet Oluştur Sayfası

*Sayfaları/Filmleri/Create.cshtml* Jilet Sayfa dosyasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio, Tag Helpers için kullanılan farklı bir kalın yazı tipinde aşağıdaki etiketleri görüntüler:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aşağıdaki Etiket Yardımcıları önceki biçimlendirmede gösterilir:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Visual Studio, Tag Helpers için kullanılan farklı bir kalın yazı tipinde aşağıdaki etiketleri görüntüler:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

Öğe `<form method="post">` bir [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)olduğunu. Form Tag Helper otomatik olarak bir [antiforgery belirteci](xref:security/anti-request-forgery)içerir.

İskele motoru, modeldeki her alan için (kimlik hariç) aşağıdakilere benzer jilet biçimlendirmeoluşturur:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Doğrulama Etiketi Yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` `<span asp-validation-for`ve ) doğrulama hatalarını görüntüler. Doğrulama daha sonra bu seride daha ayrıntılı olarak ele alınmıştır.

[Etiket Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) `<label asp-for="Movie.Title" class="control-label"></label>`( ) `for` `Title` özelliği için etiket başlığı ve öznitelik oluşturur.

[Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulama sı için gerekli HTML özniteliklerini üretir.

Tag Helpers hakkında `<form method="post">`daha fazla bilgi için , [ASP.NET](xref:mvc/views/tag-helpers/intro)bkz.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Sonraki model](xref:tutorials/razor-pages/model)
> [ekleme: Veritabanı](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğretici, [önceki öğreticide](xref:tutorials/razor-pages/model)iskele tarafından oluşturulan Razor Pages inceler.

Örneği [görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)

## <a name="the-create-delete-details-and-edit-pages"></a>Sayfaları Oluştur, Sil, Ayrıntı ve Düzenle

*Sayfaları/Filmleri/Index.cshtml.cs* Sayfa Modelini Inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Jilet Sayfaları `PageModel`türetilmiştir. Kural olarak, `PageModel`-türetilmiş `<PageName>Model`sınıf denir. Oluşturucu sayfaya eklemek için `RazorPagesMovieContext` [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection) kullanır. Tüm iskele sayfaları bu deseni takip eder. Entity Framework ile asynchronous programlama hakkında daha fazla bilgi için [Asynchronous koduna](xref:data/ef-rp/intro#asynchronous-code) bakın.

Sayfa için bir istek yapıldığında, `OnGetAsync` yöntem bir film listesini Razor Page'e döndürür. `OnGetAsync`veya `OnGet` sayfanın durumunu niçin başlatılması için Bir Jilet Sayfası'nda çağrılır. Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.

İade `OnGet` `void` veya `OnGetAsync` `Task`rirken, iade yöntemi kullanılmaz. İade türü `IActionResult` veya `Task<IActionResult>`, bir iade deyimi sağlanmalıdır. Örneğin, *Sayfalar/Filmler/Create.cshtml.cs* `OnPostAsync` yöntemi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>*Sayfaları/Filmleri/Index.cshtml* Jilet Sayfasını Inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor HTML'den C#'a veya Jilete özgü biçimlendirmeye geçiş yapabilir. Bir `@` [sembol, Jilet ayrılmış](xref:mvc/views/razor#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür, aksi takdirde C#'a dönüşür.

`@page` Razor yönergesi, dosyayı bir MVC eylemine dönüştürür, bu da istekleri işleyebilir anlamına gelir. `@page`bir sayfadaki ilk Razor direktifi olmalıdır. `@page`Jilete özgü biçimlendirmeye geçişin bir örneğidir. Daha fazla bilgi için [Razor sözdizimine](xref:mvc/views/razor#razor-syntax) bakın.

Aşağıdaki HTML Yardımcısı'nda kullanılan lambda ifadesini inceleyin:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

HTML `DisplayNameFor` Yardımcısı, görüntü `Title` adını belirlemek için lambda ifadesinde başvurulan özelliği denetler. Lambda ifadesi değerlendirilmek yerine incelenir. Bu, `model`, , , `model.Movie`veya `model.Movie[0]` boş `null` olduğunda hiçbir erişim ihlali anlamına gelir. Lambda ifadesi değerlendirildiğinde (örneğin, modelin özellik değerleri `@Html.DisplayFor(modelItem => item.Title)`ile) değerlendirilir.

<a name="md"></a>

### <a name="the-model-directive"></a>Direktif @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

Yönerge, `@model` Razor Page'e geçirilen modelin türünü belirtir. Önceki örnekte, `@model` satır `PageModel`-türetilmiş sınıfı Razor Page için kullanılabilir hale getirir. Model, sayfadaki `@Html.DisplayNameFor` HTML `@Html.DisplayFor` Yardımcıları ve [HTML Yardımcıları'nda](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) kullanılır.

### <a name="the-layout-page"></a>Düzen sayfası

Menü bağlantılarını seçin **(RazorPagesMovie,** **Home**, ve **Privacy**). Her sayfa aynı menü düzenini gösterir. Menü düzeni *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında uygulanır. *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasını açın.

[Düzen](xref:mvc/views/layout) şablonları, sitenizin HTML kapsayıcı düzenini tek bir yerde belirtmenize ve ardından sitenizdeki birden çok sayfaya uygulamanıza olanak tanır. `@RenderBody()` Hattı bul. `RenderBody`oluşturduğunuz tüm sayfaya özgü görünümlerin düzen sayfasına sarılmış olarak *görüntülendiği* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Pages/Privacy.cshtml** görünümü `RenderBody` yöntemin içinde işlenir.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData ve düzen

*Sayfalar/Filmler/Index.cshtml* dosyasındaki aşağıdaki kodu göz önünde bulundurun:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Önceki vurgulanan kod, Razor'Un C#'a geçişine bir örnektir. `{` Ve `}` karakterler C# kodu bloğunu içine alar.

Taban `PageModel` sınıfın, `ViewData` Görünüm'e geçirmek istediğiniz verileri eklemek için kullanılabilecek bir sözlük özelliği vardır. Anahtar/değer deseni `ViewData` kullanarak sözlük içine nesneler eklersiniz. Önceki örnekte, "Başlık" özelliği sözlük eklenir. `ViewData`

"Başlık" özelliği *Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında kullanılır. Aşağıdaki biçimlendirme *_Layout.cshtml* dosyasının ilk birkaç satırını gösterir.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Satır, `@*Markup removed for brevity.*@` düzen dosyanızda görünmeyen bir Razor yorumudur. HTML yorumlarının`<!-- -->`aksine ( ), Razor yorumları istemciye gönderilmez.

### <a name="update-the-layout"></a>Düzeni güncelleştirme

`<title>` *Sayfa/Paylaşılan/_Layout.cshtml* dosyasındaki öğeyi **RazorPagesMovie**yerine **Film** görüntülemek için değiştirin.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

*Sayfalar/Paylaşılan/_Layout.cshtml* dosyasında aşağıdaki bağlantı öğesini bulun.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Önceki bağlantı öğesi bir [Etiket Yardımcısı'dır.](xref:mvc/views/tag-helpers/intro) Bu durumda, [Çapa Etiketi Yardımcısı.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-page="/Movies/Index"` Tag Helper özniteliği ve `/Movies/Index` değeri, Razor Page'e bir bağlantı oluşturur. Öznitelik `asp-area` değeri boşolduğundan, alan bağlantıda kullanılmaz. Daha fazla bilgi için [Alanlar'a](xref:mvc/controllers/areas) bakın.

Değişikliklerinizi kaydedin ve **RpMovie** bağlantısına tıklayarak uygulamayı test edin. Herhangi bir sorun varsa GitHub [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.

Diğer bağlantıları test edin **(Ana Sayfa**, **RpMovie**, **Oluştur**, **Düzenle**ve **Sil**). Her sayfa, tarayıcı sekmesinde görebileceğiniz başlığı ayarlar. Bir sayfayı yer imi yaptığınızda, başlık yer imi için kullanılır.

> [!NOTE]
> `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halklar ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için, uygulamanızı küreselleştirmek için adımlar atmalısınız. Ondalık virgül ekleme yönergeleri için bu [GitHub sorunu 4076.](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)

Özellik `Layout` *Pages/_ViewStart.cshtml* dosyasında ayarlanır:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Önceki biçimlendirme, düzen dosyasını *Sayfalar* klasörü altındaki tüm Razor dosyaları için *Sayfalar/Paylaşılan/_Layout.cshtml* olarak ayarlar. Daha fazla bilgi için [Düzen'e](xref:razor-pages/index#layout) bakın.

### <a name="the-create-page-model"></a>Sayfa oluştur modeli

*Sayfalar/Filmler/Create.cshtml.cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

Yöntem, `OnGet` sayfa için gereken durumu başlangıç olarak belirtir. Create sayfasının başlatılması için herhangi bir durumu `Page` yoktur, bu nedenle döndürülür. Daha sonra öğretici `OnGet` de yöntem durum başlatma bakın. Yöntem, `Page` `PageResult` *Create.cshtml* sayfasını işleyen bir nesne oluşturur.

`Movie` Özellik, `[BindProperty]` [bağlamayı modellemek](xref:mvc/models/model-binding)için özniteliği kullanır. Form oluştur form değerlerini yayınladığında, ASP.NET Çekirdek çalışma süresi deftere `Movie` nakledilen değerleri modele bağlar.

Yöntem, `OnPostAsync` sayfa gönderileri veri oluşturduğunda çalıştırılır:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Herhangi bir model hatası varsa, form, deftere nakledilen form verileriyle birlikte yeniden görüntülenir. Çoğu model hatası form deftere nakledilmeden önce istemci tarafında yakalanabilir. Model hatasıörneği, tarih alanı için tarihe dönüştürülemeyen bir değer deftere nakletmektir. İstemci tarafı doğrulama ve model doğrulama daha sonra öğreticide ele alınmıştır.

Model hatası yoksa, veriler kaydedilir ve tarayıcı Dizin sayfasına yönlendirilir.

### <a name="the-create-razor-page"></a>Jilet Oluştur Sayfası

*Sayfaları/Filmleri/Create.cshtml* Jilet Sayfa dosyasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio `<form method="post">` etiketi Tag Helpers için kullanılan farklı bir kalın yazı tipinde görüntüler:

![Create.cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tag Helpers hakkında `<form method="post">`daha fazla bilgi için , [ASP.NET](xref:mvc/views/tag-helpers/intro)bkz.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Mac için Visual `<form method="post">` Studio etiketi Tag Helpers için kullanılan farklı bir kalın yazı tipinde görüntüler.

---

Öğe `<form method="post">` bir [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)olduğunu. Form Tag Helper otomatik olarak bir [antiforgery belirteci](xref:security/anti-request-forgery)içerir.

İskele motoru, modeldeki her alan için (kimlik hariç) aşağıdakilere benzer jilet biçimlendirmeoluşturur:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Doğrulama Etiketi Yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` `<span asp-validation-for`ve ) doğrulama hatalarını görüntüler. Doğrulama daha sonra bu seride daha ayrıntılı olarak ele alınmıştır.

[Etiket Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) `<label asp-for="Movie.Title" class="control-label"></label>`( ) `for` `Title` özelliği için etiket başlığı ve öznitelik oluşturur.

[Giriş Etiketi Yardımcısı](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulama sı için gerekli HTML özniteliklerini üretir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Önceki: Sonraki model](xref:tutorials/razor-pages/model)
> [ekleme: Veritabanı](xref:tutorials/razor-pages/sql)

::: moniker-end
