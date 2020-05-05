---
title: ASP.NET Core kısmi görünümler
author: ardalis
description: Büyük biçimlendirme dosyalarını bölmek ve ASP.NET Core uygulamalarında Web sayfalarında ortak biçimlendirmenin çoğaltılmasını azaltmak için kısmi görünümleri nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/partial
ms.openlocfilehash: 1bce6b9cdc876062b050eae6eb3c4acf0127ce92
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777130"
---
# <a name="partial-views-in-aspnet-core"></a>ASP.NET Core kısmi görünümler

[Steve Smith](https://ardalis.com/), [Maa jendoubi](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Scott Sauber](https://twitter.com/scottsauber)

Kısmi görünüm, HTML çıkışını [Razor](xref:mvc/views/razor) başka bir işaretleme dosyası tarafından oluşturulan çıkış *içinde* işleyen bir biçimlendirme dosyasıdır (*. cshtml*).

::: moniker range=">= aspnetcore-2.1"

*Kısmi görünüm* terimi, biçimlendirme dosyaları *Görünümler*olarak adlandırılan bir MVC uygulaması veya biçimlendirme dosyalarının Razor *Sayfalar*olarak adlandırıldığını bir sayfalar uygulaması geliştirirken kullanılır. Bu konu genel olarak, MVC görünümleri ve Razor sayfalar sayfalarını *biçimlendirme dosyaları*olarak gösterir.

::: moniker-end

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Kısmi görünümlerin ne zaman kullanılacağı

Kısmi görünümler şu şekilde etkili bir yoldur:

* Büyük biçimlendirme dosyalarını daha küçük bileşenlere bölün.

  Birkaç mantıksal parçadan oluşan büyük, karmaşık bir biçimlendirme dosyasında, her bir parçada kısmi bir görünümde yalıtılmış olarak çalışmanın bir avantajı vardır. Biçimlendirme dosyasındaki kod, biçimlendirme yalnızca genel sayfa yapısını ve kısmi görünümlere yönelik başvuruları içerdiğinden yönetilebilir.
* Biçimlendirme dosyaları arasında ortak biçimlendirme içeriğinin çoğaltılmasını azaltın.

  Biçimlendirme dosyalarında aynı biçimlendirme öğeleri kullanıldığında, kısmi bir görünüm biçimlendirme içeriğinin tek bir kısmi görünüm dosyasına çoğaltılmasını kaldırır. Kısmi görünümdeki biçimlendirme değiştirildiğinde, kısmi görünümü kullanan biçimlendirme dosyalarının işlenmiş çıkışını günceller.

Yaygın düzen öğelerini korumak için kısmi görünümler kullanılmamalıdır. Ortak düzen öğeleri [_Layout. cshtml](xref:mvc/views/layout) dosyalarında belirtilmelidir.

Biçimlendirmeyi işlemek için karmaşık işleme mantığının veya kod yürütmenin gerekli olduğu kısmi bir görünüm kullanmayın. Kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components)kullanın.

## <a name="declare-partial-views"></a>Kısmi görünümler bildirme

::: moniker range=">= aspnetcore-2.0"

Kısmi görünüm, *Görünümler* klasörü (MVC) veya *Sayfalar* klasörü (Razor sayfalar) içinde tutulan bir *. cshtml* biçimlendirme dosyasıdır.

ASP.NET Core MVC 'de, denetleyici bir görünüm <xref:Microsoft.AspNetCore.Mvc.ViewResult> veya kısmi görünüm döndürmektedir. Razor Sayfalarda, bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> nesne olarak temsil edilen kısmi bir görünüm döndürebilir. Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.

MVC görünümü veya sayfa işleme farklı olarak, kısmi bir görünüm *_ViewStart. cshtml*çalıştırmaz. *_ViewStart. cshtml*hakkında daha fazla bilgi için bkz <xref:mvc/views/layout>..

Kısmi görünüm dosya adları genellikle bir alt çizgi (`_`) ile başlar. Bu adlandırma kuralı gerekli değildir, ancak görünüm ve sayfalardan kısmi görünümleri görsel açıdan ayırt etmeye yardımcı olur.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Kısmi görünüm, *Görünümler* klasörü içinde tutulan bir *. cshtml* biçimlendirme dosyasıdır.

Denetleyicinin bir görünüm <xref:Microsoft.AspNetCore.Mvc.ViewResult> veya kısmi görünüm döndürme özelliği vardır. Kısmi görünümlere başvurmak ve işlemek [kısmi görünüm başvurusu](#reference-a-partial-view) bölümünde açıklanmaktadır.

MVC görünüm işlemenin aksine, kısmi bir görünüm *_ViewStart. cshtml*çalıştırmaz. *_ViewStart. cshtml*hakkında daha fazla bilgi için bkz <xref:mvc/views/layout>..

Kısmi görünüm dosya adları genellikle bir alt çizgi (`_`) ile başlar. Bu adlandırma kuralı gerekli değildir, ancak kısmen görünümlerini görünümlerde görsel açıdan ayırt etmeye yardımcı olur.

::: moniker-end

## <a name="reference-a-partial-view"></a>Kısmi görünüme başvur

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a>Razor Sayfalarda pagemodel içinde kısmi bir görünüm kullanma

ASP.NET Core 2,0 veya 2,1 ' de, aşağıdaki işleyici yöntemi * \_authorpartialrp. cshtml* kısmi görünümünü yanıta işler:

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

ASP.NET Core 2,2 veya sonraki sürümlerde, bir işleyici yöntemi alternatif olarak bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> `PartialViewResult` nesnesi oluşturmak için yöntemini çağırabilir:

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a>Biçimlendirme dosyasında kısmi görünüm kullanma

::: moniker range=">= aspnetcore-2.1"

Bir biçimlendirme dosyasında kısmi bir görünüme başvurmak için birkaç yol vardır. Uygulamaların aşağıdaki zaman uyumsuz işleme yaklaşımlardan birini kullanmasını öneririz:

* [Kısmi Etiket Yardımcısı](#partial-tag-helper)
* [Zaman uyumsuz HTML Yardımcısı](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

Bir biçimlendirme dosyasında, kısmi bir görünüme başvurmak için iki yol vardır:

* [Zaman uyumsuz HTML Yardımcısı](#asynchronous-html-helper)
* [Zaman uyumlu HTML Yardımcısı](#synchronous-html-helper)

Uygulamaların [zaman uyumsuz HTML yardımcısını](#asynchronous-html-helper)kullanmasını öneririz.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Kısmi Etiket Yardımcısı

[Kısmi etiket yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) ASP.NET Core 2,1 veya sonraki bir sürümü gerektirir.

Kısmi etiket Yardımcısı içeriği zaman uyumsuz olarak işler ve HTML benzeri bir sözdizimi kullanır:

```cshtml
<partial name="_PartialName" />
```

Bir dosya uzantısı mevcut olduğunda, etiket Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:

```cshtml
<partial name="_PartialName.cshtml" />
```

Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur. Bir tilde işareti (`~/`) veya eğik çizgi (`/`) ile başlayan yollar uygulama köküne başvurur:

**RazorSayfaları**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Zaman uyumsuz HTML Yardımcısı

Bir HTML Yardımcısı kullanırken en iyi yöntem kullanılır <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync`içinde Sarmalanan bir <xref:Microsoft.AspNetCore.Html.IHtmlContent> tür döndürür <xref:System.Threading.Tasks.Task%601>. Yöntemine, beklenen çağrının bir `@` karakterle önek olarak eklenerek başvurulur:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Dosya uzantısı varsa, HTML Yardımcısı kısmi görünümü çağıran biçimlendirme dosyasıyla aynı klasörde olması gereken kısmi bir görünüme başvurur:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

Aşağıdaki örnek, uygulama kökünden kısmi bir görünüme başvurur. Bir tilde işareti (`~/`) veya eğik çizgi (`/`) ile başlayan yollar uygulama köküne başvurur:

::: moniker range=">= aspnetcore-2.1"

**RazorSayfaları**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

Aşağıdaki örnek, göreli bir yol ile kısmi bir görünüme başvurur:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Alternatif olarak, ile <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>kısmi bir görünüm işleyebilirsiniz. Bu yöntem bir <xref:Microsoft.AspNetCore.Html.IHtmlContent>döndürmez. İşlenmiş çıktıyı doğrudan yanıta akıp. Yöntem bir sonuç döndürmediğinden, bir Razor kod bloğu içinde çağrılmalıdır:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

`RenderPartialAsync` , İçeriği oluşturduğundan, bazı senaryolarda daha iyi performans sağlar. Performans açısından kritik durumlarda, her iki yaklaşımı kullanarak sayfayı kıyaslar ve daha hızlı bir yanıt üreten yaklaşımı kullanır.

### <a name="synchronous-html-helper"></a>Zaman uyumlu HTML Yardımcısı

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*>ve <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sırasıyla zaman uyumlu `PartialAsync` eşdeğerlerdir `RenderPartialAsync`. Zaman uyumlu eşdeğerleri, kilitlendikleri senaryolar olduğu için önerilmez. Zaman uyumlu yöntemler gelecek sürümlerde kaldırılmak üzere hedeflenmiştir.

> [!IMPORTANT]
> Kodu yürütmeniz gerekiyorsa, kısmi bir görünüm yerine bir [Görünüm bileşeni](xref:mvc/views/view-components) kullanın.

::: moniker range=">= aspnetcore-2.1"

Bir `Partial` Visual `RenderPartial` Studio Çözümleyicisi uyarısıyla çağırma veya sonuç. Örneğin, varlığı aşağıdaki uyarı iletisini `Partial` verir:

> Ihtmlhelper. Partial kullanımı uygulama kilitlenmeleri oluşmasına neden olabilir. &lt;Kısmi&gt; etiket Yardımcısı veya ıhtmlhelper. partıalasync kullanmayı düşünün.

Çağrıları `@Html.Partial` Ile veya `@await Html.PartialAsync` [kısmi etiket Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)ile değiştirin. Kısmi etiket Yardımcısı geçişi hakkında daha fazla bilgi için bkz. [HTML Yardımcısı 'Ndan geçiş](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Kısmi görünüm bulma

Bir dosya uzantısı olmayan kısmi bir görünüme ad ile başvurulduğunda, aşağıdaki konumlar belirtilen sırada aranır:

::: moniker range=">= aspnetcore-2.1"

**RazorSayfaları**

1. Şu anda sayfanın klasörü yürütülüyor
1. Sayfanın klasörünün üzerindeki Dizin grafiği
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

Kısmi görünüm bulma için aşağıdaki kurallar geçerlidir:

* Kısmi görünümler farklı klasörlerde olduğunda aynı dosya adına sahip farklı kısmi görünümlere izin verilir.
* Dosya uzantısı olmadan kısmi bir görünüme ada göre başvurulması ve kısmi görünümün hem arayanın klasöründe hem de *paylaşılan* klasörde mevcut olması halinde, çağıranın klasöründeki kısmi görünüm kısmi görünümü sağlar. Kısmi görünüm çağıranın klasöründe yoksa, kısmi görünüm *paylaşılan* klasörden sağlanır. *Paylaşılan* klasördeki kısmi görünümler, *paylaşılan kısmi görünümler* veya *varsayılan kısmi görünümler*olarak adlandırılır.
* Kısmi Görünümler *zincirleme*&mdash;olabilir kısmi görünüm, çağrılar tarafından bir döngüsel başvuru oluşturulmadığı durumlarda başka bir kısmi görünümü çağırabilir. Göreli yollar her zaman geçerli dosyaya göredir, dosyanın köküne veya üst öğesine göre değil.

> [!NOTE]
> Kısmi [Razor](xref:mvc/views/razor) `section` görünümde tanımlanan bir öğe, üst biçimlendirme dosyaları için görünmez değildir. `section` Yalnızca tanımlandığı kısmi görünüm için görülebilir.

## <a name="access-data-from-partial-views"></a>Kısmi görünümlerde verilere erişin

Kısmi bir görünüm örneği oluşturulduğunda, üst öğenin `ViewData` sözlüğünün bir *kopyasını* alır. Kısmi görünüm içindeki verilerde yapılan güncelleştirmeler üst görünümde kalıcı değildir. `ViewData`kısmi görünüm geri döndüğünde kısmi görünümdeki değişiklikler kaybolur.

Aşağıdaki örnek, bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) örneğinin kısmi bir görünüme nasıl geçirileceğini göstermektedir:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Bir modeli kısmi bir görünüme geçirebilirsiniz. Model özel bir nesne olabilir. Bir modeli ile `PartialAsync` geçirebilirsiniz (bir içerik bloğunu çağırana kaydedebilir) veya `RenderPartialAsync` (içeriği çıkışa akıp):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**RazorSayfaları**

Örnek uygulamada aşağıdaki biçimlendirme, *Pages/ArticlesRP/ReadRP. cshtml* sayfasından yapılır. Sayfada iki kısmi görünüm bulunur. İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer. `ViewDataDictionary` Oluşturucu aşırı yüklemesi, var olan `ViewData` sözlüğü korurken yeni `ViewData` bir sözlüğü geçirmek için kullanılır.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

*Pages/Shared/_AuthorPartialRP. cshtml* , *readrp. cshtml* işaretleme dosyası tarafından başvurulan ilk kısmi görünümüdür:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP. cshtml* , *readrp. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümüdür:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

Örnek uygulamada aşağıdaki biçimlendirme *görünümleri/makaleleri/Read. cshtml* görünümünü gösterir. Görünüm iki kısmi görünüm içerir. İkinci kısmi görünüm bir modelde ve `ViewData` kısmi görünüme geçer. `ViewDataDictionary` Oluşturucu aşırı yüklemesi, var olan `ViewData` sözlüğü korurken yeni `ViewData` bir sözlüğü geçirmek için kullanılır.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

*Views/Shared/_AuthorPartial. cshtml* , *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ilk kısmi görünümdür:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Görünümler/makaleler/_ArticleSection. cshtml* *Read. cshtml* biçimlendirme dosyası tarafından başvurulan ikinci kısmi görünümdür:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

Çalışma zamanında, partiler, kendisini paylaşılan *_Layout. cshtml*içinde işlenen üst biçimlendirme dosyasının işlenmiş çıktısına işlenir. İlk kısmi görünüm, makalenin adını ve yayımlama tarihini işler:

> Abrayhelincoln
>
> &lt;Paylaşılan kısmi görünüm dosyası yolundan&gt;bu kısmi görünüm.
> 11/19/1863 12:00:00

İkinci kısmi görünüm, makalenin bölümlerini işler:

> Bölüm bir dizin: 0
>
> Dört puan ve yedi yıl önce...
>
> Bölüm Iki Dizin: 1
>
> Artık harika bir hukuki War, test ediyor...
>
> Bölüm üç Dizin: 2
>
> Ancak, daha büyük bir fikir için ayıramıyoruz...

## <a name="additional-resources"></a>Ek kaynaklar

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
