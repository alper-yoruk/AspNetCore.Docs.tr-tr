---
title: ASP.NET Core bölgeler
author: rick-anderson
description: Alanların ilgili işlevleri bir grup içinde ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak düzenlemek için kullanılan bir ASP.NET MVC özelliği olduğunu öğrenin.
ms.author: riande
ms.date: 03/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: 00071b91f2ef374668b74a41190d49b0be83e817
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774307"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Core bölgeler

[Dhananjay Rohan](https://twitter.com/debug_mode) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Bölgeler, ilgili işlevselliği ayrı olarak bir grup içinde düzenlemek için kullanılan bir ASP.NET özelliğidir:

* Yönlendirme için ad alanı.
* Görünümler ve Razor sayfalar için klasör yapısı.

Alanların kullanılması, başka bir rota `area`parametresi,, `controller` ve `action` veya bir Razor sayfa `page`ekleyerek yönlendirmenin amacı için bir hiyerarşi oluşturur.

Alanları, her biri kendi Razor sayfa, denetleyiciler, görünümler ve modeller kümesine sahip bir ASP.NET Core Web uygulamasını daha küçük işlevsel gruplar halinde bölümlemek için bir yol sağlar. Bir alan, bir uygulamanın içindeki yapısı etkin bir şekilde. Bir ASP.NET Core Web projesinde, sayfalar, model, denetleyici ve görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur. ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır. Büyük bir uygulama için, uygulamayı işlevlerin ayrı üst düzey alanlarında bölümlemek avantajlı olabilir. Örneğin, kullanıma alma, faturalandırma ve arama gibi birden çok iş birimi içeren bir e-ticaret uygulaması. Bu birimlerin her birinin görünümleri, denetleyicileri, Razor sayfaları ve modelleri içermesi için kendi alanı vardır.

Şu durumlarda bir projedeki alanı kullanmayı göz önünde bulundurun:

* Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.
* Her işlevsel alanın bağımsız olarak çalışabilmesi için uygulamayı bölümlemek istiyorsunuz.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)). İndirme örneği, test bölgeleri için temel bir uygulama sağlar.

Sayfalar kullanıyorsanız Razor , bkz. bu belgede [sayfaları bulunan Razor bölgeler](#areas-with-razor-pages) .

## <a name="areas-for-controllers-with-views"></a>Görünümlere sahip denetleyiciler için bölgeler

Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core Web uygulaması şunları içerir:

* Bir [alan klasörü yapısı](#area-folder-structure).
* Denetleyiciyi içeren [`[Area]`](#attribute) ve denetleyiciyi alanla ilişkilendiren denetleyiciler:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Başlangıç alanına eklenen alan yolu](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Alan klasörü yapısı

İki mantıksal grup, *ürün* ve *hizmet*içeren bir uygulamayı düşünün. Alan kullanarak, klasör yapısı aşağıdakine benzer olacaktır:

* Proje adı
  * Alanlar
    * Ürünler
      * Denetleyiciler
        * HomeController.cs
        * ManageController.cs
      * Görünümler
        * Giriş
          * Index.cshtml
        * Yönetme
          * Index.cshtml
          * . Cshtml hakkında
    * Hizmetler
      * Denetleyiciler
        * HomeController.cs
      * Görünümler
        * Giriş
          * Index.cshtml

Yukarıdaki düzen, alan kullanılırken tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir. Eşleşen bir alan görünümü dosyası için bulma aramalarını aşağıdaki sırayla görüntüleyin:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Denetleyiciyi bir alanla ilişkilendir

Alan denetleyicileri, [ &lbrack;alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliğiyle belirlenir:

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Alan yolu Ekle

Alan rotaları genellikle [öznitelik yönlendirme](xref:mvc/controllers/routing#ar)yerine [geleneksel yönlendirmeyi](xref:mvc/controllers/routing#cr) kullanır. Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.

`{area:...}`, URL alanı tüm alanlarda Tekdüzen ise yol şablonlarında bir belirteç olarak kullanılabilir:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

Önceki kodda, `exists` yolun bir alanla eşleşmesi gereken bir kısıtlama uygular. İle `{area:...}` `MapControllerRoute`kullanma:

* Alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.
* `[Area("Area name")]` Özniteliği ile tüm denetleyicilerle eşleşir.

Aşağıdaki kod, iki <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> adlandırılmış alan yolları oluşturmak için kullanır:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Daha fazla bilgi için bkz. [alan yönlendirme](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>MVC alanlarıyla bağlantı oluşturma

[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) aşağıdaki kodu, belirtilen alanla birlikte bağlantı oluşturmayı gösterir:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Örnek indirme, aşağıdakileri içeren [kısmi bir görünüm](xref:mvc/views/partial) içerir:

* Önceki bağlantılar.
* Önceki `area` bir ile benzer bağlantılar belirtilmedi.

Kısmi görünüme, [Düzen dosyasında](xref:mvc/views/layout)başvurulur, bu nedenle uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alan ve denetleyicideki bir sayfadan başvuruluyorsa geçerlidir.

Alan veya denetleyici belirtilmediğinde, yönlendirme [ortam](xref:mvc/controllers/routing#ambient) değerlerine göre değişir. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanmak, alanı belirtmeyen biçimlendirme ile yanlış bağlantılar oluşturur.

Daha fazla bilgi için bkz. [Denetleyici eylemlerine yönlendirme](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart. cshtml dosyasını kullanan alanların paylaşılan düzeni

Uygulamanın tamamı için ortak bir düzen paylaşmak üzere, [uygulama kök klasöründe](#arf) *_ViewStart. cshtml* 'yi saklayın. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Uygulama kök klasörü

Uygulama kök klasörü, ASP.NET Core şablonları ile oluşturulan Web uygulamasındaki *Startup.cs* içeren klasördür.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

 */Views/_ViewImports. cshtml*, MVC için */pages/_ViewImports. cshtml* for Razor Pages, alanlardaki görünümlere aktarılmaz. Tüm görünümlere görünüm içeri aktarmaları sağlamak için aşağıdaki yaklaşımlardan birini kullanın:

* [Uygulama kök klasörüne](#arf) *_ViewImports. cshtml* ekleyin. Uygulama kök klasöründeki bir *_ViewImports. cshtml* uygulamadaki tüm görünümlere uygulanır.
* *_ViewImports. cshtml* dosyasını, alanlarda uygun görünüm klasörüne kopyalayın.

*_ViewImports. cshtml* dosyası genellikle [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) içeri aktarmaları, `@using`, ve `@inject` deyimlerini içerir. Daha fazla bilgi için bkz. [paylaşılan yönergeleri Içeri aktarma](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Görünümlerin depolandığı varsayılan alan klasörünü değiştirme

Aşağıdaki kod varsayılan alan klasörünü ' den `"Areas"` ' a değiştirir `"MyAreas"`:

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Sayfaları olan Razor bölgeler

Sayfaları olan Razor alanlarda, uygulamanın `Areas/<area name>/Pages` kökünde bir klasör gerekir. Aşağıdaki klasör yapısı [örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)birlikte kullanılır:

* Proje adı
  * Alanlar
    * Ürünler
      * Sayfalar
        * _ViewImports
        * Hakkında
        * Dizin oluşturma
    * Hizmetler
      * Sayfalar
        * Yönetme
          * Hakkında
          * Dizin oluşturma

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Sayfalar ve alanlarla bağlantı oluşturma

[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kodu, belirtilen alanla birlikte bağlantı oluşturmayı gösterir (örneğin, `asp-area="Products"`):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Örnek indirme, önceki bağlantıları içeren [kısmi bir görünüm](xref:mvc/views/partial) ve alanı belirtmeksizin aynı bağlantıları içerir. Kısmi görünüme, [Düzen dosyasında](xref:mvc/views/layout)başvurulur, bu nedenle uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvuruluyorsa geçerlidir.

Alan belirtilmediğinde, yönlendirme *ortam* değerlerine göre değişir. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanmak yanlış bağlantılar oluşturur. Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Önceki kod için:

* Öğesinden `<a asp-page="/Manage/About">` oluşturulan bağlantı, yalnızca son istek alanındaki bir sayfa `Services` için olduğunda doğrudur. Örneğin `/Services/Manage/` `/Services/Manage/Index`,, veya `/Services/Manage/About`.
* Öğesinden `<a asp-page="/About">` oluşturulan bağlantı yalnızca son istek içindeki `/Home`bir sayfa için olduğunda doğrudur.
* Kod, [örnek indirden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Ad alanı ve etiket yardımcıları _ViewImports dosya ile içeri aktarma

Bir *_ViewImports. cshtml* dosyası her bir alan *sayfaları* klasörüne eklenerek, ad alanı ve etiket yardımcıları, klasördeki her Razor bir sayfaya alınır.

Örnek kodun bir *_ViewImports. cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun. Aşağıdaki biçimlendirme */Services/Manage/about* Razor sayfasını göstermektedir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Önceki biçimlendirmede:

* Modeli belirtmek için tam etki alanı adının kullanılması gerekir (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Örnek indirme sırasında, ürünler alanı aşağıdaki *_ViewImports. cshtml* dosyasını içerir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Aşağıdaki biçimlendirme */Products/about* Razor sayfasını göstermektedir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

Önceki dosyada, ad alanı ve `@addTagHelper` yönerge, *alan/ürün/sayfa/_ViewImports. cshtml* dosyası tarafından dosyaya aktarılır.

Daha fazla bilgi için bkz. [etiket Yardımcısı kapsamını yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sayfalar için Razor paylaşılan düzen alanı

Uygulamanın tamamında ortak bir düzen paylaşmak için *_ViewStart. cshtml* 'yi uygulama kök klasörüne taşıyın.

### <a name="publishing-areas"></a>Yayımlama alanı

*Wwwroot* dizinindeki tüm *. cshtml dosyaları ve dosyaları, *. csproj dosyasına dahil edildiğinde `<Project Sdk="Microsoft.NET.Sdk.Web">` çıkışa yayımlanır.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bölgeler, ilgili işlevselliği ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak bir grup içinde düzenlemek için kullanılan bir ASP.NET özelliğidir. Alanların kullanılması, başka bir rota `area`parametresi,, `controller` ve `action` veya bir Razor sayfa `page`ekleyerek yönlendirmenin amacı için bir hiyerarşi oluşturur.

Alanları, her biri kendi Razor sayfa, denetleyiciler, görünümler ve modeller kümesine sahip bir ASP.NET Core Web uygulamasını daha küçük işlevsel gruplar halinde bölümlemek için bir yol sağlar. Bir alan, bir uygulamanın içindeki yapısı etkin bir şekilde. Bir ASP.NET Core Web projesinde, sayfalar, model, denetleyici ve görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur. ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır. Büyük bir uygulama için, uygulamayı işlevlerin ayrı üst düzey alanlarında bölümlemek avantajlı olabilir. Örneğin, kullanıma alma, faturalandırma ve arama gibi birden çok iş birimi içeren bir e-ticaret uygulaması. Bu birimlerin her birinin görünümleri, denetleyicileri, Razor sayfaları ve modelleri içermesi için kendi alanı vardır.

Şu durumlarda bir projedeki alanı kullanmayı göz önünde bulundurun:

* Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.
* Her işlevsel alanın bağımsız olarak çalışabilmesi için uygulamayı bölümlemek istiyorsunuz.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)). İndirme örneği, test bölgeleri için temel bir uygulama sağlar.

Sayfalar kullanıyorsanız Razor , bkz. bu belgede [sayfaları bulunan Razor bölgeler](#areas-with-razor-pages) .

## <a name="areas-for-controllers-with-views"></a>Görünümlere sahip denetleyiciler için bölgeler

Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core Web uygulaması şunları içerir:

* Bir [alan klasörü yapısı](#area-folder-structure).
* Denetleyiciyi içeren [`[Area]`](#attribute) ve denetleyiciyi alanla ilişkilendiren denetleyiciler:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Başlangıç alanına eklenen alan yolu](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Alan klasörü yapısı

İki mantıksal grup, *ürün* ve *hizmet*içeren bir uygulamayı düşünün. Alan kullanarak, klasör yapısı aşağıdakine benzer olacaktır:

* Proje adı
  * Alanlar
    * Ürünler
      * Denetleyiciler
        * HomeController.cs
        * ManageController.cs
      * Görünümler
        * Giriş
          * Index.cshtml
        * Yönetme
          * Index.cshtml
          * . Cshtml hakkında
    * Hizmetler
      * Denetleyiciler
        * HomeController.cs
      * Görünümler
        * Giriş
          * Index.cshtml

Yukarıdaki düzen, alan kullanılırken tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir. Eşleşen bir alan görünümü dosyası için bulma aramalarını aşağıdaki sırayla görüntüleyin:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Denetleyiciyi bir alanla ilişkilendir

Alan denetleyicileri, [ &lbrack;alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliğiyle belirlenir:

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Alan yolu Ekle

Alan rotaları genellikle öznitelik yönlendirme yerine geleneksel yönlendirmeyi kullanır. Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.

`{area:...}`, URL alanı tüm alanlarda Tekdüzen ise yol şablonlarında bir belirteç olarak kullanılabilir:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

Önceki kodda, `exists` yolun bir alanla eşleşmesi gereken bir kısıtlama uygular. Kullanmak `{area:...}` , alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.

Aşağıdaki kod, iki <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> adlandırılmış alan yolları oluşturmak için kullanır:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

ASP.NET Core 2,2 `MapAreaRoute` ile kullanırken, [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/7772)bakın.

Daha fazla bilgi için bkz. [alan yönlendirme](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>MVC alanlarıyla bağlantı oluşturma

[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) aşağıdaki kodu, belirtilen alanla birlikte bağlantı oluşturmayı gösterir:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Yukarıdaki kodla oluşturulan bağlantılar, uygulamanın herhangi bir yerinde geçerlidir.

Örnek indirme, önceki bağlantıları içeren [kısmi bir görünüm](xref:mvc/views/partial) ve alanı belirtmeksizin aynı bağlantıları içerir. Kısmi görünüme, [Düzen dosyasında](xref:mvc/views/layout)başvurulur, bu nedenle uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alan ve denetleyicideki bir sayfadan başvuruluyorsa geçerlidir.

Alan veya denetleyici belirtilmediğinde, yönlendirme *ortam* değerlerine göre değişir. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanmak yanlış bağlantılar oluşturur.

Daha fazla bilgi için bkz. [Denetleyici eylemlerine yönlendirme](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart. cshtml dosyasını kullanan alanların paylaşılan düzeni

Uygulamanın tamamında ortak bir düzen paylaşmak için *_ViewStart. cshtml* 'yi uygulama kök klasörüne taşıyın.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

Standart konumunda */views/_ViewImports. cshtml* , alanlara uygulanmaz. Ortak [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), `@using`veya `@inject` bölgenizde kullanmak için, [alan Görünümleriniz için](xref:mvc/views/layout#importing-shared-directives)uygun bir *_ViewImports. cshtml* dosyasının geçerli olduğundan emin olun. Tüm görünümlerinizin aynı davranışını istiyorsanız, */views/_ViewImports. cshtml* öğesini uygulama köküne taşıyın.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Görünümlerin depolandığı varsayılan alan klasörünü değiştirme

Aşağıdaki kod varsayılan alan klasörünü ' den `"Areas"` ' a değiştirir `"MyAreas"`:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Sayfaları olan Razor bölgeler

Sayfaları olan Razor alanlarda, uygulamanın `Areas/<area name>/Pages` kökünde bir klasör gerekir. Aşağıdaki klasör yapısı [örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)birlikte kullanılır:

* Proje adı
  * Alanlar
    * Ürünler
      * Sayfalar
        * _ViewImports
        * Hakkında
        * Dizin oluşturma
    * Hizmetler
      * Sayfalar
        * Yönetme
          * Hakkında
          * Dizin oluşturma

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Sayfalar ve alanlarla bağlantı oluşturma

[Örnek indirmenin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kodu, belirtilen alanla birlikte bağlantı oluşturmayı gösterir (örneğin, `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Yukarıdaki kodla oluşturulan bağlantılar, uygulamanın herhangi bir yerinde geçerlidir.

Örnek indirme, önceki bağlantıları içeren [kısmi bir görünüm](xref:mvc/views/partial) ve alanı belirtmeksizin aynı bağlantıları içerir. Kısmi görünüme, [Düzen dosyasında](xref:mvc/views/layout)başvurulur, bu nedenle uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvuruluyorsa geçerlidir.

Alan belirtilmediğinde, yönlendirme *ortam* değerlerine göre değişir. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanmak yanlış bağlantılar oluşturur. Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Önceki kod için:

* Öğesinden `<a asp-page="/Manage/About">` oluşturulan bağlantı, yalnızca son istek alanındaki bir sayfa `Services` için olduğunda doğrudur. Örneğin `/Services/Manage/` `/Services/Manage/Index`,, veya `/Services/Manage/About`.
* Öğesinden `<a asp-page="/About">` oluşturulan bağlantı yalnızca son istek içindeki `/Home`bir sayfa için olduğunda doğrudur.
* Kod, [örnek indirden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Ad alanı ve etiket yardımcıları _ViewImports dosya ile içeri aktarma

Bir *_ViewImports. cshtml* dosyası her bir alan *sayfaları* klasörüne eklenerek, ad alanı ve etiket yardımcıları, klasördeki her Razor bir sayfaya alınır.

Örnek kodun bir *_ViewImports. cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun. Aşağıdaki biçimlendirme */Services/Manage/about* Razor sayfasını göstermektedir:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Önceki biçimlendirmede:

* Modeli belirtmek için tam etki alanı adının kullanılması gerekir (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Örnek indirme sırasında, ürünler alanı aşağıdaki *_ViewImports. cshtml* dosyasını içerir:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Aşağıdaki biçimlendirme */Products/about* Razor sayfasını göstermektedir:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

Önceki dosyada, ad alanı ve `@addTagHelper` yönerge, *alan/ürün/sayfa/_ViewImports. cshtml* dosyası tarafından dosyaya aktarılır.

Daha fazla bilgi için bkz. [etiket Yardımcısı kapsamını yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve [paylaşılan yönergeleri içeri aktarma](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sayfalar için Razor paylaşılan düzen alanı

Uygulamanın tamamında ortak bir düzen paylaşmak için *_ViewStart. cshtml* 'yi uygulama kök klasörüne taşıyın.

### <a name="publishing-areas"></a>Yayımlama alanı

*Wwwroot* dizinindeki tüm *. cshtml dosyaları ve dosyaları, *. csproj dosyasına dahil edildiğinde `<Project Sdk="Microsoft.NET.Sdk.Web">` çıkışa yayımlanır.
::: moniker-end
