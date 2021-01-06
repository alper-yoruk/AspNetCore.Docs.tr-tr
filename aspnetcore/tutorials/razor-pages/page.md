---
title: Bölüm 3, yapı iskelesi Razor sayfaları
author: rick-anderson
description: Sayfalardaki eğitim serisinin 3. bölümü Razor .
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a6efbb22f8b6280bd636cd1575d8a4a2bca0bb06
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486180"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Bölüm 3, ASP.NET Core yapı iskelesi Razor olan sayfalar

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide Razor , [önceki öğreticide](xref:tutorials/razor-pages/model)yapı iskelesi tarafından oluşturulan sayfalar incelenir.

::: moniker range=">= aspnetcore-5.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a>Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları

*Pages/filmler/ Index . cshtml.cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Sayfalar öğesinden türetilir `PageModel` . Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` . Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `RazorPagesMovieContext` :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .

Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür Razor . Sayfasında Razor , `OnGetAsync` veya `OnGet` sayfanın durumunu başlatmak için çağırılır. Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.

Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , Return deyimleri kullanılmaz. Örneğin gizlilik sayfası:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir. Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>*Pages/filmler/ Index . cshtml* Razor sayfasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir Razor . Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.

### <a name="the-page-directive"></a>@pageYönergesi

`@page` Razor Yönergesi, dosyayı bir MVC eylemi yapar, bu da istekleri işleyebileceği anlamına gelir. `@page` sayfadaki ilk yönerge olmalıdır Razor . `@page` ve `@model` belirli biçimlendirme örneklerine örnek olarak verilebilir Razor . Daha fazla bilgi için bkz. [ Razor sözdizimi](xref:mvc/views/razor#razor-syntax) .

<a name="md"></a>

### <a name="the-model-directive"></a>@modelYönergesi

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir Razor . Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir Razor . Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.


Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler. Lambda ifadesi değerlendirilmek yerine incelenir. Bu,,, veya boş olduğunda bir erişim ihlali olmadığı anlamına gelir `model` `model.Movie` `model.Movie[0]` `null` . Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.

### <a name="the-layout-page"></a>Düzen sayfası

**Razor Pagesfilmi**, **ana sayfası** ve **gizliliği** bağlayan menüyü seçin. Her sayfada aynı menü düzeni gösterilir. Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.

*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.

[Düzen](xref:mvc/views/layout) ŞABLONLARı, HTML kapsayıcı düzeninin şu şekilde olmasını sağlar:

* Tek bir yerde belirtildi.
* Sitede birden çok sayfada uygulandı.

Satırı bulun `@RenderBody()` . `RenderBody` , sayfaya özgü tüm görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçin ve *Sayfalar/gizlilik. cshtml* görünümü yöntemin içinde işlenir `RenderBody` .

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData ve Layout

*Sayfalar/filmler/ Index . cshtml* dosyasından aşağıdaki biçimlendirmeyi göz önünde bulundurun:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Önceki vurgulanan biçimlendirme C# ' ye geçme örneğidir Razor . `{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.

`PageModel`Temel sınıf, `ViewData` verileri bir görünüme geçirmek için kullanılabilecek bir Dictionary özelliği içerir. Nesneler, `ViewData` bir ***anahtar değeri** _ düzeniyle kullanılarak sözlüğe eklenir. Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.

`Title`Özelliği, _Pages/Shared/_Layout. cshtml * dosyasında kullanılır. Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

Satır `@*Markup removed for brevity.*@` bir Razor açıklamadır. HTML yorumlarından farklı olarak `<!-- -->` , Razor açıklamalar istemciye gönderilmez. Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.

### <a name="update-the-layout"></a>Düzeni güncelleştirme

1. `<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **Razor pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. *Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. Önceki öğeyi aşağıdaki biçimlendirmeyle değiştirin:

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro). Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir. `asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` Razor . `asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz. Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.

1. **Rpmovie** bağlantısını seçerek değişiklikleri kaydedin ve uygulamayı test edin. Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dosyasına bakın.

1. **Giriş**, **rpmovie**, **oluşturma**, **düzenleme** ve **silme** bağlantılarını test edin. Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.

> [!NOTE]
> Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir. Ondalık virgülden ekleme hakkında yönergeler için bkz. [GitHub sorunu 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .

`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar Razor .  Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Sayfa oluştur modeli

*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır. Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür. Öğreticide daha sonra, `OnGet` başlatma durumuna bir örnek gösterilir. `Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.

`Movie`Özelliği, [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) özniteliğini kullanır. Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.

Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir. Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir. Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme. İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.

Model hatası yoksa:

* Veriler kaydedilir.
* Tarayıcı Index sayfaya yönlendirilir.

### <a name="the-create-no-locrazor-page"></a>Oluştur Razor sayfası

*Sayfalar/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio, etiket yardımcıları için kullanılan farklı kalın yazı tipiyle aşağıdaki etiketleri görüntüler:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create. cshtml sayfasının VS17 görünümü](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Aşağıdaki etiket yardımcıları, önceki biçimlendirmede gösterilmektedir:

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper). Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.

Yapı iskelesi altyapısı, Razor modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler. Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.

Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .

[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.

Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a>Oluşturma, silme, Ayrıntılar ve düzenleme sayfaları

*Pages/filmler/ Index . cshtml.cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Sayfalar öğesinden türetilir `PageModel` . Kural gereği, `PageModel` -türetilmiş sınıf adlandırılır `<PageName>Model` . Oluşturucu, sayfasına eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `RazorPagesMovieContext` . Yapı iskelesi sayfaları bu düzene uyar. Entity Framework zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [zaman uyumsuz kod](xref:data/ef-rp/intro#asynchronous-code) .

Sayfa için bir istek yapıldığında, `OnGetAsync` yöntemi sayfaya bir film listesi döndürür Razor . `OnGetAsync` ya da sayfa `OnGet` Razor için durumu başlatmak üzere bir sayfada çağırılır. Bu durumda, `OnGetAsync` filmlerin bir listesini alır ve görüntüler.

Döndürüldüğünde `OnGet` `void` veya `OnGetAsync` döndüğünde `Task` , return yöntemi kullanılmaz. Dönüş türü `IActionResult` veya olduğunda `Task<IActionResult>` , bir return ifadesinin sağlanması gerekir. Örneğin, *Pages/filmler/Create. cshtml. cs* `OnPostAsync` yöntemi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a>*Pages/filmler/ Index . cshtml* Razor sayfasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor HTML 'den C# veya belirli bir biçimlendirmeye geçiş yapabilir Razor . Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](xref:mvc/views/razor#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeye geçiş yapar, aksi takdirde C# içine geçiş yapar.

`@page` Razor Yönergesi, dosyayı bir MVC eylemine yapar, bu da istekleri işleyebileceği anlamına gelir. `@page` sayfadaki ilk yönerge olmalıdır Razor . `@page` , belirli bir biçimlendirmeyi geçme örneğidir Razor . Daha fazla bilgi için bkz. [ Razor sözdizimi](xref:mvc/views/razor#razor-syntax) .

<a name="md"></a>

### <a name="the-model-directive"></a>@modelYönergesi

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

`@model`Yönergesi, sayfaya geçirilen modelin türünü belirtir Razor . Yukarıdaki örnekte, `@model` satır `PageModel` türetilen sınıfı sayfa için kullanılabilir hale getirir Razor . Model, `@Html.DisplayNameFor` sayfadaki ve `@Html.DisplayFor` [HTML yardımcılarını](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) sayfasında kullanılır.

### <a name="html-helpers"></a>HTML Yardımcıları

Aşağıdaki HTML Yardımcısı 'nda kullanılan lambda ifadesini inceleyin:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType>HTML Yardımcısı, `Title` görünen adı belirlemede lambda ifadesinde başvurulan özelliği inceler. Lambda ifadesi değerlendirilmek yerine incelenir. Diğer bir deyişle `model` ,, `model.Movie` , veya boş olduğunda erişim ihlali `model.Movie[0]` yoktur `null` . Lambda ifadesi değerlendirildiğinde, örneğin ile, `@Html.DisplayFor(modelItem => item.Title)` modelin özellik değerleri değerlendirilir.
### <a name="the-layout-page"></a>Düzen sayfası

**Razor Pagesfilmi**, **ana sayfası** ve **gizliliği** bağlayan menüyü seçin. Her sayfada aynı menü düzeni gösterilir. Menü düzeni *sayfa/paylaşılan/_Layout. cshtml* dosyasında uygulanır.

*Sayfalar/paylaşılan/_Layout. cshtml* dosyasını açın ve inceleyin.

[Düzen](xref:mvc/views/layout) şablonları, BIR sitenin HTML kapsayıcı yerleşimini tek bir yerde belirtmenize ve sonra sitede birden çok sayfaya uygulamanıza olanak tanır. Satırı bulun `@RenderBody()` . `RenderBody` , oluşturduğunuz tüm sayfaya özgü görünümlerin, Düzen sayfasında *kaydırılan* bir yer tutucudur. Örneğin, **Gizlilik** bağlantısını seçerseniz, **Sayfa/Gizlilik. cshtml** görünümü yöntemin içinde işlenir `RenderBody` .

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData ve Layout

*Sayfalar/filmler/ Index . cshtml* dosyasından aşağıdaki kodu göz önünde bulundurun:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Önceki vurgulanan kod C# ' ye geçme örneğidir Razor . `{`Ve `}` karakterleri bir C# kodu bloğunu kapsar.

`PageModel`Temel sınıfın, `ViewData` bir görünüme geçirmek istediğiniz verileri eklemek için kullanılabilecek bir Dictionary özelliği vardır. `ViewData`Bir anahtar/değer örüntüsünün kullanıldığı sözlüğe nesneler eklersiniz. Yukarıdaki örnekte, `Title` özelliği `ViewData` sözlüğe eklenir.

`Title`Özelliği *Pages/Shared/_Layout. cshtml* dosyasında kullanılır. Aşağıdaki biçimlendirme *_Layout. cshtml* dosyasının ilk birkaç satırını gösterir.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

Satır `@*Markup removed for brevity.*@` bir Razor açıklamadır. HTML yorumlarından farklı olarak `<!-- -->` , Razor açıklamalar istemciye gönderilmez. Daha fazla bilgi için bkz. [MDN Web belgeleri: HTML ile çalışmaya](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) başlama.

### <a name="update-the-layout"></a>Düzeni güncelleştirme

`<title>` *Pages/Shared/_Layout. cshtml* dosyasındaki öğeyi **Razor pagesmovie** yerine **filmi** görüntüleyecek şekilde değiştirin.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

*Sayfa/paylaşılan/_Layout. cshtml* dosyasında aşağıdaki tutturucu öğeyi bulun.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Önceki öğeyi aşağıdaki biçimlendirme ile değiştirin.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

Önceki tutturucu öğesi bir [etiket yardımcıdır](xref:mvc/views/tag-helpers/intro). Bu durumda, [bağlantı etiketi yardımcısının](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)olması gerekir. `asp-page="/Movies/Index"`Etiket Yardımcısı özniteliği ve değeri sayfaya bir bağlantı oluşturur `/Movies/Index` Razor . `asp-area`Öznitelik değeri boş olduğundan, alan bağlantıda kullanılmaz. Daha fazla bilgi için bkz. [alanlara](xref:mvc/controllers/areas) bakın.

Değişikliklerinizi kaydedin ve **Rpmovie** bağlantısına tıklayarak uygulamayı test edin. Herhangi bir sorununuz varsa GitHub 'daki [_Layout. cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dosyasına bakın.

Diğer bağlantıları test edin (**giriş**, **rpmovie**, **oluşturma**, **düzenleme** ve **silme**). Her sayfada, tarayıcı sekmesinde görebileceğiniz başlık ayarlanır. Bir sayfada yer işareti eklediğinizde başlık, yer işareti için kullanılır.

> [!NOTE]
> Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamayı globalize için gerekli adımları uygulamanız gerekir. Bu GitHub, ondalık virgülden ekleme hakkında yönergeler için [4076 sorun](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) .

`Layout`Özelliği, *Pages/_ViewStart. cshtml* dosyasında ayarlanır:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Yukarıdaki biçimlendirme, düzen dosyasını sayfalar klasörü altındaki tüm dosyalar için *Sayfalar/paylaşılan/_Layout. cshtml* olarak ayarlar Razor .  Daha fazla bilgi için bkz. [Düzen](xref:razor-pages/index#layout) .

### <a name="the-create-page-model"></a>Sayfa oluştur modeli

*Pages/filmler/Create. cshtml. cs* sayfa modelini inceleyin:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

`OnGet`Yöntemi, sayfa için gereken tüm durumları başlatır. Oluşturma sayfasında, başlatılacak durum yoktur, bu nedenle `Page` döndürülür. Öğreticide daha sonra `OnGet` Yöntem başlatma durumunu görürsünüz. `Page`Yöntemi `PageResult` *Create. cshtml* sayfasını işleyen bir nesne oluşturur.

`Movie`Özelliği, <xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> [model bağlamayı](xref:mvc/models/model-binding)kabul etmek Için [[BindProperty]] özniteliğini kullanır. Oluşturma formu form değerlerini gönderirse, ASP.NET Core çalışma zamanı, gönderilen değerleri `Movie` modele bağlar.

Bu `OnPostAsync` Yöntem, sayfa form verileri göndertiğinde çalıştırılır:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Herhangi bir model hatası varsa, form, gönderilen tüm form verileriyle birlikte yeniden görüntülenir. Form gönderilmeden önce çoğu model hatası istemci tarafında yakalanabilir. Bir model hatasına bir örnek, Date alanı için bir tarihe dönüştürülemeyen bir değer gönderme. İstemci tarafı doğrulama ve model doğrulaması Öğreticinin ilerleyen kısımlarında ele alınmıştır.

Model hatası yoksa, veriler kaydedilir ve tarayıcı Index sayfaya yönlendirilir.

### <a name="the-create-no-locrazor-page"></a>Oluştur Razor sayfası

*Sayfalar/filmler/Create. cshtml* Razor sayfa dosyasını inceleyin:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio etiketi, etiket `<form method="post">` yardımcıları için kullanılan farklı bir kalın yazı tipiyle görüntüler:

![Create. cshtml sayfasının VS17 görünümü](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Gibi etiket yardımcıları hakkında daha fazla bilgi için `<form method="post">` bkz. [ASP.NET Core etiket yardımcıları](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Mac için Visual Studio etiket `<form method="post">` yardımcıları için kullanılan farklı kalın yazı tipinde etiketi görüntüler.

---

`<form method="post">`Öğesi bir [form etiketi yardımcıdır](xref:mvc/views/working-with-forms#the-form-tag-helper). Form etiketi Yardımcısı, bir [antiforgery belirtecini](xref:security/anti-request-forgery)otomatik olarak içerir.

Yapı iskelesi altyapısı, Razor modeldeki her alan için, kimliği dışında, aşağıdakine benzer şekilde biçimlendirme oluşturur:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Doğrulama etiketi yardımcıları](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` ve `<span asp-validation-for` ) doğrulama hatalarını görüntüler. Doğrulama, bu serinin ilerleyen kısımlarında daha ayrıntılı bir şekilde ele alınmıştır.

Etiket [etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ), özelliğin etiket açıklamalı alt yazısını ve `[for]` özniteliğini oluşturur `Title` .

[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ), [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci TARAFıNDA jQuery doğrulaması için gerekli HTML özniteliklerini üretir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Önceki: model ekleme](xref:tutorials/razor-pages/model) 
>  [Sonraki: bir veritabanıyla çalışma](xref:tutorials/razor-pages/sql)

::: moniker-end
