---
title: ASP.NET Core düzen
author: ardalis
description: Bir ASP.NET Core uygulamasında görünümler işlemeden önce ortak düzenleri kullanmayı, yönergeleri paylaşmayı ve ortak kodu çalıştırmayı öğrenin.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 4d5032f02db28341d7781dd57d58d776636fd16d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020450"
---
# <a name="layout-in-aspnet-core"></a>ASP.NET Core düzen

[Steve Smith](https://ardalis.com/) ve [bave Brock](https://twitter.com/daveabrock) tarafından

Sayfalar ve görünümler genellikle görsel ve programlı öğeleri paylaşır. Bu makalede nasıl yapılacağı gösterilmektedir:

* Ortak düzenleri kullanın.
* Komutları paylaşma.
* Sayfaları veya görünümleri işlemeden önce ortak kodu çalıştırın.

Bu belge, ASP.NET Core MVC için iki farklı yaklaşım için düzenleri açıklar: Razor görünümler içeren sayfalar ve denetleyiciler. Bu konu için, farklar en az:

* RazorSayfalar, *Sayfalar* klasöründedir.
* Görünümleri olan denetleyiciler görünümler için bir *Görünümler* klasörü kullanır.

## <a name="what-is-a-layout"></a>Düzen nedir?

Çoğu Web uygulaması, bir sayfadan sayfaya gezindikleri sürece kullanıcıya tutarlı bir deneyim sağlayan ortak bir düzene sahiptir. Düzen genellikle uygulama üstbilgisi, gezinti veya menü öğeleri ve alt bilgi gibi ortak kullanıcı arabirimi öğelerini içerir.

![Sayfa düzeni örneği](layout/_static/page-layout.png)

Betikler ve stil sayfaları gibi ortak HTML yapıları da bir uygulama içindeki birçok sayfa tarafından sık kullanılır. Bu paylaşılan öğelerin tümü, bir *Düzen* dosyasında tanımlanabilir ve bu daha sonra uygulama içinde kullanılan herhangi bir görünüm tarafından başvurulabilirler. Düzenler görünümlerde yinelenen kodu azaltır.

Kurala göre, bir ASP.NET Core uygulamasının varsayılan düzeni *_Layout. cshtml*olarak adlandırılır. Şablonlarla oluşturulan yeni ASP.NET Core projelerine yönelik düzen dosyaları şunlardır:

* RazorSayfalar: *sayfa/paylaşılan/_Layout. cshtml*

  ![Çözüm Gezgini sayfa klasörü](layout/_static/rp-web-project-views.png)

* Görünümler içeren denetleyici: *views/Shared/_Layout. cshtml*

  ![Çözüm Gezgini içindeki görünümler klasörü](layout/_static/mvc-web-project-views.png)

Düzen, uygulamadaki görünümler için üst düzey bir şablon tanımlar. Uygulamalar bir düzen gerektirmez. Uygulamalar, farklı düzenleri belirten farklı görünümlerle birden fazla düzen tanımlayabilir.

Aşağıdaki kod, bir şablon tarafından oluşturulan ve bir denetleyici ve görünümleri olan bir proje için Düzen dosyasını gösterir:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Düzen belirtme

RazorGörünümler bir `Layout` özelliğe sahiptir. Bireysel görünümler bu özelliği ayarlayarak bir düzen belirtir:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Belirtilen Düzen tam yol (örneğin, */Pages/Shared/_Layout. cshtml* veya */views/Shared/_Layout. cshtml*) ya da kısmi bir ad kullanabilir (örnek: `_Layout` ). Kısmi bir ad sağlandığında, Razor Görünüm altyapısı, kendi standart bulma işlemini kullanarak düzen dosyasını arar. Önce işleyici yönteminin (veya denetleyicinin) bulunduğu klasör, sonra *paylaşılan* klasör tarafından aranır. Bu bulma işlemi, [kısmi görünümleri](xref:mvc/views/partial#partial-view-discovery)bulmak için kullanılan işlemle aynıdır.

Varsayılan olarak, tüm mizanpajın çağırması gerekir `RenderBody` . Çağrısının yerleştirildiği her yerde `RenderBody` , görünümün içerikleri işlenir.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>Bölümler

Bir düzen, çağırarak, isteğe bağlı olarak bir veya daha fazla *bölüme*başvurabilir `RenderSection` . Bölümler, belirli sayfa öğelerinin yerleştirilmesi gereken yerleri düzenlemek için bir yol sağlar. Her çağrısı, `RenderSection` Bu bölümün gerekli veya isteğe bağlı olup olmadığını belirtebilir:

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

Gerekli bir bölüm bulunamazsa, bir özel durum oluşturulur. Tek görünümler, sözdizimini kullanarak bir bölüm içinde işlenecek içeriği belirtir `@section` Razor . Bir sayfa veya görünüm bir bölümü tanımlıyorsa, oluşturulması gerekir (veya bir hata oluşur).

`@section`Sayfalar görünümünde örnek tanım Razor :

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

Yukarıdaki kodda *betikler/main.js* `scripts` sayfa veya görünümdeki bölümüne eklenir. Aynı uygulamadaki diğer sayfalar veya görünümler bu betiği gerektirmeyebilir ve betikler bölümü tanımlamaz.

Aşağıdaki biçimlendirme *_ValidationScriptsPartial. cshtml*öğesini Işlemek Için [kısmi etiket yardımcısını](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) kullanır:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Önceki biçimlendirme [Yapı iskelesi Identity ](xref:security/authentication/scaffold-identity)tarafından oluşturuldu.

Bir sayfada veya görünümde tanımlanan bölümler yalnızca kendi düzen sayfasında kullanılabilir. Parçalardan başvurulamaz, bileşenleri veya görünüm sisteminin diğer kısımlarını bunlara başvuramaz.

### <a name="ignoring-sections"></a>Bölümler yoksayılıyor

Varsayılan olarak, içerik sayfasındaki gövde ve tüm bölümler Düzen sayfası tarafından işlenmelidir. RazorGörünüm altyapısı, gövdenin ve her bölümün işlenip işlenmeyeceğini izleyerek bunu zorlar.

Görünüm altyapısına gövde veya bölümleri yok saymasını bildirmek için `IgnoreBody` ve `IgnoreSection` yöntemlerini çağırın.

Bir sayfadaki gövde ve her bölüm Razor işlenen ya da yoksayıldı olmalıdır.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Paylaşılan yönergeler içeri aktarılıyor

Görünümler ve sayfalar, Razor ad alanlarını içeri aktarmak ve [bağımlılık ekleme](dependency-injection.md)'yi kullanmak için yönergeleri kullanabilir. Birçok görünüm tarafından paylaşılan yönergeler, ortak bir *_ViewImports. cshtml* dosyasında belirtilebilir. `_ViewImports`Dosya aşağıdaki yönergeleri destekler:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Dosya Razor , işlevler ve bölüm tanımları gibi diğer özellikleri desteklemez.

Örnek `_ViewImports.cshtml` Dosya:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

ASP.NET Core MVC uygulamasının *_ViewImports. cshtml* dosyası genellikle *Sayfalar* (veya *Görünümler*) klasörüne yerleştirilir. Bir *_ViewImports. cshtml* dosyası herhangi bir klasöre yerleştirilebilir, bu durumda yalnızca söz konusu klasör ve alt klasörleri içindeki sayfalara veya görünümlere uygulanacaktır. `_ViewImports`dosyalar, kök düzeyinden başlayarak işlenir ve sonra her bir klasör için sayfanın konumu veya görünümü görüntülenir. `_ViewImports`kök düzeyinde belirtilen ayarlar klasör düzeyinde geçersiz kılınabilir.

Örneğin, şunu varsayın:

* Kök düzeyi *_ViewImports. cshtml* dosyası ve içerir `@model MyModel1` `@addTagHelper *, MyTagHelper1` .
* Bir alt klasör *_ViewImports. cshtml* dosyası `@model MyModel2` ve içerir `@addTagHelper *, MyTagHelper2` .

Alt klasördeki sayfaların ve görünümlerin her ikisi de etiket yardımcılarını ve modeline erişimi olur `MyModel2` .

Dosya hiyerarşisinde birden çok *_ViewImports. cshtml* dosyası bulunursa, yönergelerin birleştirilmiş davranışı şunlardır:

* `@addTagHelper`, `@removeTagHelper` : tüm çalıştırma, sırasıyla
* `@tagHelperPrefix`: görünümün en yakın olanı, diğerlerini geçersiz kılar
* `@model`: görünümün en yakın olanı, diğerlerini geçersiz kılar
* `@inherits`: görünümün en yakın olanı, diğerlerini geçersiz kılar
* `@using`: tümü dahildir; yinelemeler yoksayıldı
* `@inject`: her bir özellik için, görünümün en yakın olanı aynı özellik adına sahip diğer diğerlerini geçersiz kılar

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Her görünümden önce kod çalıştırma

Her görünüm veya sayfadan önce çalıştırılması gereken kod *_ViewStart. cshtml* dosyasına yerleştirilmelidir. Kurala göre, *_ViewStart. cshtml* dosyası *Sayfalar* (veya *Görünümler*) klasöründe bulunur. *_ViewStart. cshtml* 'de listelenen deyimler her tam görünüm (düzen değil ve kısmi görünümler değil) öncesinde çalıştırılır. [Viewwimports. cshtml](xref:mvc/views/layout#viewimports)gibi *_ViewStart. cshtml* de hiyerarşiktir. Görünüm veya sayfalar klasöründe bir *_ViewStart. cshtml* dosyası tanımlanmışsa, *Sayfalar* (veya *Görünümler*) klasörünün kökünde (varsa) tanımlandıktan sonra çalıştırılır.

Örnek bir *_ViewStart. cshtml* dosyası:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Yukarıdaki dosya tüm görünümlerin *_Layout. cshtml* mizanpajını kullanacağı belirtir.

*_ViewStart. cshtml* ve *_ViewImports. cshtml* genellikle */Pages/Shared* (veya */views/Shared*) klasörüne **yerleştirilmez** . Bu dosyaların uygulama düzeyi sürümleri doğrudan */Pages* (veya */views*) klasörüne yerleştirilmelidir.
