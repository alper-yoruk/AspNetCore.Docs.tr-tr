---
title: ASP.NET Çekirdek'teki Alanlar
author: rick-anderson
description: Alanların, ilgili işlevleri ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak grup halinde düzenlemek için kullanılan ASP.NET bir MVC özelliğinin nasıl olduğunu öğrenin.
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625924"
---
# <a name="areas-in-aspnet-core"></a>ASP.NET Çekirdek'teki Alanlar

Yazar: [Dhananjay Kumar](https://twitter.com/debug_mode) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Alanlar, ilgili işlevselliği ayrı olarak bir gruba düzenlemek için kullanılan ASP.NET bir özelliktir:

* Yönlendirme için ad alanı.
* Görünümler ve Jilet Sayfaları için klasör yapısı.

`area`Alanları kullanmak, başka bir rota parametresi, `controller` `action` yani Jilet Sayfası `page`ekleyerek yönlendirme amacıyla bir hiyerarşi oluşturur.

Alanlar, ASP.NET Core Web uygulamasını her biri kendi Jilet Sayfaları, denetleyicileri, görünümleri ve modelleri olan daha küçük işlevsel gruplara bölmenin bir yolunu sağlar. Alan, etkin bir uygulama nın içindeki bir yapıdır. ASP.NET Core web projesinde, Sayfalar, Model, Denetleyici ve Görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur. ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır. Büyük bir uygulama için, uygulamayı ayrı üst düzey işlevsellik alanlarına bölmek avantajlı olabilir. Örneğin, ödeme, faturalandırma ve arama gibi birden çok iş birimine sahip bir e-ticaret uygulaması. Bu birimlerin her birinin görünümleri, denetleyicileri, Jilet Sayfaları ve modelleri içeren kendi alanı vardır.

Şu anda projedeki Alanları kullanmayı düşünün:

* Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.
* Uygulamayı, her işlevsel alanın bağımsız olarak çalışabilmesi için bölmek istiyorsunuz.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample) İndirme örneği, test alanları için temel bir uygulama sağlar.

Jilet Sayfaları kullanıyorsanız, bu belgede [Jilet Li Alanlar'a](#areas-with-razor-pages) bakın.

## <a name="areas-for-controllers-with-views"></a>Görünüme sahip denetleyiciler için alanlar

Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core web uygulaması aşağıdakileri içerir:

* Alan [klasör yapısı.](#area-folder-structure)
* Denetleyiciyi [`[Area]`](#attribute) alanla ilişkilendirecek öznitelikteki denetleyiciler:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Başlangıç için eklenen alan rotası:](#add-area-route)

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Alan klasör yapısı

İki mantıksal grubu olan bir uygulamayı düşünün, *Ürünler* ve *Hizmetler.* Alanları kullanarak, klasör yapısı aşağıdakilere benzer olacaktır:

* Proje adı
  * Alanlar
    * Ürünler
      * Denetleyiciler
        * HomeController.cs
        * ManageController.cs
      * Görünümler
        * Ev
          * Index.cshtml
        * Yönetme
          * Index.cshtml
          * Hakkında.cshtml
    * Hizmetler
      * Denetleyiciler
        * HomeController.cs
      * Görünümler
        * Ev
          * Index.cshtml

Alanlar kullanılırken önceki düzen tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir. Eşleşen bir alan görünümü dosyasını aşağıdaki sırada bulma aramalarını görüntüle:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Denetleyiciyi bir Alanla ilişkilendirin

Alan denetleyicileri [ &lbrack;Alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği ile belirlenir:

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Alan rota ekle

Alan yolları genellikle [öznitelik yönlendirmeyerine](xref:mvc/controllers/routing#ar) [geleneksel yönlendirme](xref:mvc/controllers/routing#cr) kullanın. Konvansiyonel yönlendirme isteðe baðlð±r. Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.

`{area:...}`url alanı tüm alanlarda tek düze yse rota şablonlarında belirteç olarak kullanılabilir:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

Önceki kodda, `exists` rotanın bir alanla eşleşmesi gereken bir kısıtlama uygulanır. `MapControllerRoute`Kullanarak: `{area:...}`

* Alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.
* Tüm denetleyicileri öznitelik ile `[Area("Area name")]` eşleşir.

Aşağıdaki kod, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> iki adlandırılmış alan yolu oluşturmak için kullanır:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Daha fazla bilgi için [Bkz. Alan](xref:mvc/controllers/routing#areas)yönlendirmesi.

### <a name="link-generation-with-mvc-areas"></a>MVC alanları ile bağlantı oluşturma

[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) aşağıdaki kod, belirtilen alana bağlantı oluşturmayı gösterir:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Örnek karşıdan yükleme, aşağıdakileri içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir:

* Önceki bağlantılar.
* Önceki ne benzer bağlantılar `area` hariç belirtilmemiştir.

Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki ve denetleyicideki bir sayfadan başvurulduğunda geçerlidir.

Alan veya denetleyici belirtilmediğinde, yönlendirme [ortam](xref:mvc/controllers/routing#ambient) değerlerine bağlıdır. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanarak alanı belirtmeyen biçimlendirme ile yanlış bağlantılar oluşturur.

Daha fazla bilgi [için, eylemleri denetlemek için Yönlendirme'ye](xref:mvc/controllers/routing)bakın.

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart.cshtml dosyasını kullanan Alanlar için paylaşılan düzen

Uygulamanın tamamı için ortak bir düzeni paylaşmak için *_ViewStart.cshtml'i* [uygulama kökü klasöründe](#arf)tutun. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Uygulama kök klasörü

Uygulama kökü klasörü, ASP.NET Core şablonları ile oluşturulan web uygulamasında *Startup.cs* içeren klasördür.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */Views/_ViewImports.cshtml*, MVC için ve */Pages/_ViewImports.cshtml* for Razor Pages, alanlardaki görünümlere aktarılmaz. Tüm görünümlere görünüm içeriaklarını sağlamak için aşağıdaki yaklaşımlardan birini kullanın:

* [Uygulama kök klasörüne](#arf) *_ViewImports.cshtml* ekleyin. Uygulama kökü klasöründeki *bir _ViewImports.cshtml* uygulamadaki tüm görünümler için geçerli olacaktır.
* *_ViewImports.cshtml* dosyasını alanların altındaki uygun görünüm klasörüne kopyalayın.

*_ViewImports.cshtml* dosyası genellikle [Tag Helpers](xref:mvc/views/tag-helpers/intro) `@using`içeri `@inject` aktarımları ve deyimleri içerir. Daha fazla bilgi için [bkz.](xref:mvc/views/layout#importing-shared-directives)

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Görünümlerin depolandığı varsayılan alan klasörünü değiştirme

Aşağıdaki kod varsayılan alan klasörünü aşağıdakilerden `"Areas"` şu şekilde `"MyAreas"`değiştirir:

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Jilet Sayfalı Alanlar

Razor Pages içeren `Areas/<area name>/Pages` alanlar, uygulamanın kökünde bir klasör gerektirir. Örnek [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)ile aşağıdaki klasör yapısı kullanılır:

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

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Pages ve alanları ile bağlantı oluşturma

[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kod, belirtilen alana sahip bağlantı `asp-area="Products"`oluşturmayı gösterir (örneğin,):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir. Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvurulduğunda geçerlidir.

Alan belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur. Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Önceki kod için:

* Oluşturulan bağlantı `<a asp-page="/Manage/About">` yalnızca son istek alandaki `Services` bir sayfa için olduğunda doğrudur. Örneğin, `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`veya .
* Oluşturulan bağlantı `<a asp-page="/About">` yalnızca son istek bir sayfa için `/Home`olduğunda doğrudur.
* Kod [örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>_ViewImports dosyayla ad alanı ve Tag Yardımcıları alma

Klasördeki her Razor Page'e ad alanı ve Tag Helpers'ı almak için her alan *Sayfaları* klasörüne *bir _ViewImports.cshtml* dosyası eklenebilir.

Örnek kodun *_ViewImports.cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun. Aşağıdaki biçimlendirme */Hizmetler/Yönet/Hakkında* Jilet Sayfasını gösterir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Önceki biçimlendirmede:

* Modeli belirtmek için tam nitelikli alan adı`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kullanılmalıdır ( ).
* [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Örnek indirmede, Ürünler alanı aşağıdaki *_ViewImports.cshtml* dosyasını içerir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Aşağıdaki biçimlendirme */Ürünler/Jilet Hakkında* Sayfayı gösterir:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

Önceki dosyada, ad alanı `@addTagHelper` ve yönerge *Alanlar/Ürünler/Sayfalar/_ViewImports.cshtml* dosyası tarafından dosyaya aktarılır.

Daha fazla bilgi için [Bkz. Etiket Yardımcısı kapsamını Yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve Paylaşılan Yönergeleri [Alma.](xref:mvc/views/layout#importing-shared-directives)

### <a name="shared-layout-for-razor-pages-areas"></a>Jilet Sayfaları Alanları için paylaşılan düzen

Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.

### <a name="publishing-areas"></a>Yayın Alanları

*Wwwroot* dizinindeki tüm *.cshtml dosyaları ve dosyaları `<Project Sdk="Microsoft.NET.Sdk.Web">` *.csproj dosyasına dahil edildiğinde çıktıya yayınlanır.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Alanlar, ilgili işlevleri ayrı bir ad alanı (yönlendirme için) ve klasör yapısı (görünümler için) olarak grup halinde düzenlemek için kullanılan ASP.NET bir özelliktir. `area`Alanları kullanmak, başka bir rota parametresi, `controller` `action` yani Jilet Sayfası `page`ekleyerek yönlendirme amacıyla bir hiyerarşi oluşturur.

Alanlar, ASP.NET Core Web uygulamasını her biri kendi Jilet Sayfaları, denetleyicileri, görünümleri ve modelleri olan daha küçük işlevsel gruplara bölmenin bir yolunu sağlar. Alan, etkin bir uygulama nın içindeki bir yapıdır. ASP.NET Core web projesinde, Sayfalar, Model, Denetleyici ve Görünüm gibi mantıksal bileşenler farklı klasörlerde tutulur. ASP.NET Core çalışma zamanı, bu bileşenler arasındaki ilişkiyi oluşturmak için adlandırma kurallarını kullanır. Büyük bir uygulama için, uygulamayı ayrı üst düzey işlevsellik alanlarına bölmek avantajlı olabilir. Örneğin, ödeme, faturalandırma ve arama gibi birden çok iş birimine sahip bir e-ticaret uygulaması. Bu birimlerin her birinin görünümleri, denetleyicileri, Jilet Sayfaları ve modelleri içeren kendi alanı vardır.

Şu anda projedeki Alanları kullanmayı düşünün:

* Uygulama, mantıksal olarak ayrılabilen birden çok üst düzey işlevsel bileşenden oluşur.
* Uygulamayı, her işlevsel alanın bağımsız olarak çalışabilmesi için bölmek istiyorsunuz.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample) İndirme örneği, test alanları için temel bir uygulama sağlar.

Jilet Sayfaları kullanıyorsanız, bu belgede [Jilet Li Alanlar'a](#areas-with-razor-pages) bakın.

## <a name="areas-for-controllers-with-views"></a>Görünüme sahip denetleyiciler için alanlar

Alanları, denetleyicileri ve görünümleri kullanan tipik bir ASP.NET Core web uygulaması aşağıdakileri içerir:

* Alan [klasör yapısı.](#area-folder-structure)
* Denetleyiciyi [`[Area]`](#attribute) alanla ilişkilendirecek öznitelikteki denetleyiciler:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Başlangıç için eklenen alan rotası:](#add-area-route)

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Alan klasör yapısı

İki mantıksal grubu olan bir uygulamayı düşünün, *Ürünler* ve *Hizmetler.* Alanları kullanarak, klasör yapısı aşağıdakilere benzer olacaktır:

* Proje adı
  * Alanlar
    * Ürünler
      * Denetleyiciler
        * HomeController.cs
        * ManageController.cs
      * Görünümler
        * Ev
          * Index.cshtml
        * Yönetme
          * Index.cshtml
          * Hakkında.cshtml
    * Hizmetler
      * Denetleyiciler
        * HomeController.cs
      * Görünümler
        * Ev
          * Index.cshtml

Alanlar kullanılırken önceki düzen tipik olsa da, bu klasör yapısını kullanmak için yalnızca görünüm dosyaları gereklidir. Eşleşen bir alan görünümü dosyasını aşağıdaki sırada bulma aramalarını görüntüle:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Denetleyiciyi bir Alanla ilişkilendirin

Alan denetleyicileri [ &lbrack;Alan&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği ile belirlenir:

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Alan rota ekle

Alan yolları genellikle öznitelik yönlendirmeyerine geleneksel yönlendirme kullanır. Konvansiyonel yönlendirme isteðe baðlð±r. Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.

`{area:...}`url alanı tüm alanlarda tek düze yse rota şablonlarında belirteç olarak kullanılabilir:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

Önceki kodda, `exists` rotanın bir alanla eşleşmesi gereken bir kısıtlama uygulanır. Kullanma, `{area:...}` alanlara yönlendirme eklemek için en az karmaşık mekanizmadır.

Aşağıdaki kod, <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> iki adlandırılmış alan yolu oluşturmak için kullanır:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

core `MapAreaRoute` 2.2 ASP.NET kullanırken, [bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/7772)bakın.

Daha fazla bilgi için [Bkz. Alan](xref:mvc/controllers/routing#areas)yönlendirmesi.

### <a name="link-generation-with-mvc-areas"></a>MVC alanları ile bağlantı oluşturma

[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) aşağıdaki kod, belirtilen alana bağlantı oluşturmayı gösterir:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Önceki kodla oluşturulan bağlantılar uygulamanın her yerinde geçerlidir.

Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir. Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki ve denetleyicideki bir sayfadan başvurulduğunda geçerlidir.

Alan veya denetleyici belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur.

Daha fazla bilgi [için, eylemleri denetlemek için Yönlendirme'ye](xref:mvc/controllers/routing)bakın.

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>_ViewStart.cshtml dosyasını kullanan Alanlar için paylaşılan düzen

Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

Standart konumunda *, /Views/_ViewImports.cshtml* alanlar için geçerli değildir. Yaygın [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)kullanmak `@using`için `@inject` , veya bölgenizde, uygun bir *_ViewImports.cshtml* dosyası [bölge görünümleriniz için geçerli olduğundan](xref:mvc/views/layout#importing-shared-directives)emin olun. Tüm görünümlerinizde aynı davranışı istiyorsanız, */Views/_ViewImports.cshtml'i* uygulama köküne taşıyın.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Görünümlerin depolandığı varsayılan alan klasörünü değiştirme

Aşağıdaki kod varsayılan alan klasörünü aşağıdakilerden `"Areas"` şu şekilde `"MyAreas"`değiştirir:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Jilet Sayfalı Alanlar

Razor Pages içeren `Areas/<area name>/Pages` alanlar, uygulamanın kökünde bir klasör gerektirir. Örnek [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)ile aşağıdaki klasör yapısı kullanılır:

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

### <a name="link-generation-with-razor-pages-and-areas"></a>Razor Pages ve alanları ile bağlantı oluşturma

[Örnek indirmeden](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) aşağıdaki kod, belirtilen alana sahip bağlantı `asp-area="Products"`oluşturmayı gösterir (örneğin,):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Önceki kodla oluşturulan bağlantılar uygulamanın her yerinde geçerlidir.

Örnek karşıdan yükleme, alanı belirtmeden önceki bağlantıları ve aynı bağlantıları içeren kısmi bir [görünüm](xref:mvc/views/partial) içerir. Kısmi görünüm [düzen dosyasında](xref:mvc/views/layout)başvurulyur, böylece uygulamadaki her sayfa oluşturulan bağlantıları görüntüler. Alanı belirtmeden oluşturulan bağlantılar yalnızca aynı alandaki bir sayfadan başvurulduğunda geçerlidir.

Alan belirtilmediğinde, yönlendirme *ortam* değerlerine bağlıdır. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Örnek uygulama için birçok durumda, ortam değerlerini kullanarak yanlış bağlantılar oluşturur. Örneğin, aşağıdaki koddan oluşturulan bağlantıları göz önünde bulundurun:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Önceki kod için:

* Oluşturulan bağlantı `<a asp-page="/Manage/About">` yalnızca son istek alandaki `Services` bir sayfa için olduğunda doğrudur. Örneğin, `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`veya .
* Oluşturulan bağlantı `<a asp-page="/About">` yalnızca son istek bir sayfa için `/Home`olduğunda doğrudur.
* Kod [örnek indir](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>_ViewImports dosyayla ad alanı ve Tag Yardımcıları alma

Klasördeki her Razor Page'e ad alanı ve Tag Helpers'ı almak için her alan *Sayfaları* klasörüne *bir _ViewImports.cshtml* dosyası eklenebilir.

Örnek kodun *_ViewImports.cshtml* dosyası içermeyen *Hizmetler* alanını göz önünde bulundurun. Aşağıdaki biçimlendirme */Hizmetler/Yönet/Hakkında* Jilet Sayfasını gösterir:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

Önceki biçimlendirmede:

* Modeli belirtmek için tam nitelikli alan adı`@model RPareas.Areas.Services.Pages.Manage.AboutModel`kullanılmalıdır ( ).
* [Tag Yardımcıları](xref:mvc/views/tag-helpers/intro) tarafından etkinleştirilir`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Örnek indirmede, Ürünler alanı aşağıdaki *_ViewImports.cshtml* dosyasını içerir:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Aşağıdaki biçimlendirme */Ürünler/Jilet Hakkında* Sayfayı gösterir:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

Önceki dosyada, ad alanı `@addTagHelper` ve yönerge *Alanlar/Ürünler/Sayfalar/_ViewImports.cshtml* dosyası tarafından dosyaya aktarılır.

Daha fazla bilgi için [Bkz. Etiket Yardımcısı kapsamını Yönetme](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) ve Paylaşılan Yönergeleri [Alma.](xref:mvc/views/layout#importing-shared-directives)

### <a name="shared-layout-for-razor-pages-areas"></a>Jilet Sayfaları Alanları için paylaşılan düzen

Uygulamanın tamamı için ortak bir düzen paylaşmak için *_ViewStart.cshtml'i* uygulama kökü klasörüne taşıyın.

### <a name="publishing-areas"></a>Yayın Alanları

*Wwwroot* dizinindeki tüm *.cshtml dosyaları ve dosyaları `<Project Sdk="Microsoft.NET.Sdk.Web">` *.csproj dosyasına dahil edildiğinde çıktıya yayınlanır.
::: moniker-end
