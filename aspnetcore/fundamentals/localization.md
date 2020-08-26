---
title: ASP.NET Core Genelleştirme ve yerelleştirme
author: rick-anderson
description: ASP.NET Core farklı diller ve kültürlere içerik yerelleştirilmesi için nasıl hizmet ve ara yazılım sağladığını öğrenin.
ms.author: riande
ms.date: 11/30/2019
no-loc:
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
uid: fundamentals/localization
ms.openlocfilehash: d5be01c67f455a2706a654574ef9f5e273551e70
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865442"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>ASP.NET Core Genelleştirme ve yerelleştirme

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)

Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar. ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.

Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir. Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir. Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.

Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir. Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.

Uygulama yerelleştirmesi şunları içerir:

1. Uygulamanın içeriğini yerelleştirilebilir yapın
1. Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın
1. Her istek için dil/kültür seçmek üzere bir strateji uygulayın

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Uygulamanın içeriğini yerelleştirilebilir yapın

<xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır. `IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır. Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir. `IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez. Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur. Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir. Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir. Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz. Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın. Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz. Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir. Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.

`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın. `IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor. Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.

Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz. Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır. Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Yerelleştirmeyi görüntüle

`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar. `ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur. Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur. Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur. `ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz. Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz. Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Bir Fransızca kaynak dosyası şunları içerebilir:

| Anahtar | Değer |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Dataaçıklamaların yerelleştirilmesi

Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` . Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:

* *Resources/Viewmodeller. account. RegisterViewModel. fr. resx*
* *Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir. ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Birden çok sınıf için bir kaynak dizesi kullanma

Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır. Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın

### <a name="supportedcultures-and-supporteduicultures"></a>Supportedkültürleri ve SupportedUICultures

ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` . İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler. `SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler. Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . , `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler. `ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` . .NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur. ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler. Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.

## <a name="resource-files"></a>Kaynak dosyalar

Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır. Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır. Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz. "es", Ispanyolca için dil kodudur. Bu kaynak dosyasını Visual Studio 'da oluşturmak için:

1. **Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır. İkinci bağlamsal menü, yeni öğe komutunun vurgulandığı ekleme için açıktır.](localization/_static/newi.png)

1. **Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. **Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   Visual Studio, *Welcome. es. resx* dosyasını gösterir.

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a>Kaynak dosyası adlandırma

Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır. Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır. Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır. Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur. Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.

Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur. Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz. Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır. `ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek. İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır. Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.

| Kaynak adı | Nokta veya yol adlandırma |
| ------------   | ------------- |
| Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta  |
| Kaynaklar/denetleyiciler/HomeController. fr. resx  | Yol |

Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler. Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir. Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden. `ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:

* Kaynaklar/görünümler/giriş/about. fr. resx

* Kaynaklar/görünümler. Home. about. fr. resx

`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar. 

> [!WARNING]
> Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir. Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` . 

Bir derlemenin kök ad alanı, derleme adından farklıysa:

* Yerelleştirme varsayılan olarak çalışmaz.
* Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur. `RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir. 

, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.

## <a name="culture-fallback-behavior"></a>Kültür geri dönüş davranışı

Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir. İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner. Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder. Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir. Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir. Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.

Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün. Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)

Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir. Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler. Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio ile kaynak dosyaları oluşturma

Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur. ASP.NET Core, genellikle istediğiniz gibi değildir. Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur. *. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz. Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.

### <a name="add-other-cultures"></a>Diğer kültürleri Ekle

Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir. ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**). Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Her istek için dil/kültür seçmek üzere bir strateji uygulayın

### <a name="configure-localization"></a>Yerelleştirmeyi yapılandırma

Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler. Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.

* `AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler. Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır. Örneğin, *Index. fr. cshtml* dosyasındaki "fr".

* `AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .

### <a name="localization-middleware"></a>Yerelleştirme ara yazılımı

Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır. Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir. Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır. Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır. Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Varsayılan liste, en çok belirli olan en az özel. Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz. Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır. Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` . Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` . Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar. Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie . `MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .

, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür. Varsayılan cookie ad `.AspNetCore.Culture` .

cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:

```
c=en-UK|uic=en-US
```

Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.

### <a name="the-accept-language-http-header"></a>Accept-Language HTTP üstbilgisi

[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir. Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir. Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE 'de Accept-Language HTTP üstbilgisini ayarlama

1. Dişli simgesinden **Internet seçenekleri**' ne dokunun.

1. **Diller**' e dokunun.

   ![Internet seçenekleri](localization/_static/lang.png)

1. **Dil tercihlerini ayarla**' ya dokunun.

1. **Dil ekle**' ye dokunun.

1. Dilini ekleyin.

1. Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.

### <a name="use-a-custom-provider"></a>Özel bir sağlayıcı kullan

Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım. Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz. Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.

### <a name="set-the-culture-programmatically"></a>Kültürü program aracılığıyla ayarlama

[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` . *Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Yöntemi kültürü ayarlar cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz. [GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.

## <a name="model-binding-route-data-and-query-strings"></a>Model bağlama yolu verileri ve sorgu dizeleri

[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.

## <a name="globalization-and-localization-terms"></a>Genelleştirme ve yerelleştirme koşulları

Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir. Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar. Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.

[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.

Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur. Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir. Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır. Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder. Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.

Larındaki

* Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.
* Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.
* Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.
* Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.
* Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür. (örneğin, "en", "es")
* Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür. (örneğin, "en-US", "en-GB", "es-CL")
* Üst kültür: belirli bir kültürü içeren nötr kültür. (örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)
* Yerel ayar: bir yerel ayar kültür ile aynıdır.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .
* [.NET uygulamalarını genelleştirmek ve yerelleştirme](/dotnet/standard/globalization-localization/index)
* [.resx Dosyalarındaki Kaynaklar](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft çok dilli uygulama araç seti](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Yerelleştirme & genel türler](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)

Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar. ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.

Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir. Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir. Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.

Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir. Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.

Uygulama yerelleştirmesi şunları içerir:

1. Uygulamanın içeriğini yerelleştirilebilir yapın
1. Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın
1. Her istek için dil/kültür seçmek üzere bir strateji uygulayın

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Uygulamanın içeriğini yerelleştirilebilir yapın

<xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır. `IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır. Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir. `IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez. Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur. Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir. Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir. Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz. Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın. Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz. Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir. Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.

`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın. `IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor. Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.

Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz. Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır. Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Yerelleştirmeyi görüntüle

`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar. `ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur. Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur. Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur. `ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz. Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz. Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Bir Fransızca kaynak dosyası şunları içerebilir:

| Anahtar | Değer |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Dataaçıklamaların yerelleştirilmesi

Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` . Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:

* *Resources/Viewmodeller. account. RegisterViewModel. fr. resx*
* *Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir. ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Birden çok sınıf için bir kaynak dizesi kullanma

Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır. Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın

### <a name="supportedcultures-and-supporteduicultures"></a>Supportedkültürleri ve SupportedUICultures

ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` . İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler. `SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler. Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . , `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler. `ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` . .NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur. ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler. Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.

## <a name="resource-files"></a>Kaynak dosyalar

Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır. Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır. Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz. "es", Ispanyolca için dil kodudur. Bu kaynak dosyasını Visual Studio 'da oluşturmak için:

1. **Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır. İkinci bağlamsal menü, yeni öğe komutunun vurgulandığı ekleme için açıktır.](localization/_static/newi.png)

1. **Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. **Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   Visual Studio, *Welcome. es. resx* dosyasını gösterir.

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a>Kaynak dosyası adlandırma

Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır. Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır. Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır. Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur. Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.

Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur. Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz. Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır. `ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek. İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır. Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.

| Kaynak adı | Nokta veya yol adlandırma |
| ------------   | ------------- |
| Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta  |
| Kaynaklar/denetleyiciler/HomeController. fr. resx  | Yol |

Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler. Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir. Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden. `ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:

* Kaynaklar/görünümler/giriş/about. fr. resx

* Kaynaklar/görünümler. Home. about. fr. resx

`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar. 

> [!WARNING]
> Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir. Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` . 

Bir derlemenin kök ad alanı, derleme adından farklıysa:

* Yerelleştirme varsayılan olarak çalışmaz.
* Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur. `RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir. 

, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.

## <a name="culture-fallback-behavior"></a>Kültür geri dönüş davranışı

Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir. İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner. Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder. Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir. Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir. Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.

Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün. Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)

Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir. Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler. Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio ile kaynak dosyaları oluşturma

Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur. ASP.NET Core, genellikle istediğiniz gibi değildir. Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur. *. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz. Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.

### <a name="add-other-cultures"></a>Diğer kültürleri Ekle

Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir. ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**). Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Her istek için dil/kültür seçmek üzere bir strateji uygulayın

### <a name="configure-localization"></a>Yerelleştirmeyi yapılandırma

Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler. Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.

* `AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler. Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır. Örneğin, *Index. fr. cshtml* dosyasındaki "fr".

* `AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .

### <a name="localization-middleware"></a>Yerelleştirme ara yazılımı

Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır. Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir. Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır. Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır. Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Varsayılan liste, en çok belirli olan en az özel. Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz. Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır. Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` . Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` . Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar. Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie . `MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .

, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür. Varsayılan cookie ad `.AspNetCore.Culture` .

cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:

```
c=en-UK|uic=en-US
```

Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.

### <a name="the-accept-language-http-header"></a>Accept-Language HTTP üstbilgisi

[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir. Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir. Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE 'de Accept-Language HTTP üstbilgisini ayarlama

1. Dişli simgesinden **Internet seçenekleri**' ne dokunun.

1. **Diller**' e dokunun.

   ![Internet seçenekleri](localization/_static/lang.png)

1. **Dil tercihlerini ayarla**' ya dokunun.

1. **Dil ekle**' ye dokunun.

1. Dilini ekleyin.

1. Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.

### <a name="use-a-custom-provider"></a>Özel bir sağlayıcı kullan

Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım. Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz. Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.

### <a name="set-the-culture-programmatically"></a>Kültürü program aracılığıyla ayarlama

[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` . *Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Yöntemi kültürü ayarlar cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz. [GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.

## <a name="model-binding-route-data-and-query-strings"></a>Model bağlama yolu verileri ve sorgu dizeleri

[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.

## <a name="globalization-and-localization-terms"></a>Genelleştirme ve yerelleştirme koşulları

Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir. Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar. Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.

[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.

Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur. Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir. Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır. Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder. Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.

Larındaki

* Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.
* Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.
* Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.
* Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.
* Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür. (örneğin, "en", "es")
* Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür. (örneğin, "en-US", "en-GB", "es-CL")
* Üst kültür: belirli bir kültürü içeren nötr kültür. (örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)
* Yerel ayar: bir yerel ayar kültür ile aynıdır.

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .
* [.NET uygulamalarını genelleştirmek ve yerelleştirme](/dotnet/standard/globalization-localization/index)
* [.resx Dosyalarındaki Kaynaklar](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft çok dilli uygulama araç seti](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Yerelleştirme & genel türler](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Hemien Bowden](https://twitter.com/damien_bod), [Bart calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [fiham bin ateya](https://twitter.com/hishambinateya)

Çok dilli bir Web sitesi, sitenin daha geniş bir hedef kitleye ulaşmasını sağlar. ASP.NET Core, farklı diller ve kültürlere yerelleştirme için hizmet ve ara yazılım sağlar.

Uluslararası duruma getirme, [Genelleştirme](/dotnet/api/system.globalization) ve [Yerelleştirme](/dotnet/standard/globalization-localization/localization)içerir. Genelleştirme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir. Genelleştirme, belirli coğrafi alanlarla ilgili olarak tanımlanmış bir dil betikleri kümesinin giriş, görüntüleme ve çıkışı için destek ekler.

Yerelleştirme, belirli bir kültür/yerel ayara, Yerelleştirilebilirlik için zaten işlenmiş olan bir Genelleştirilmiş uygulamasını uyarlatma işlemidir. Daha fazla bilgi için bu belgenin sonundaki **Genelleştirme ve yerelleştirme koşullarına** bakın.

Uygulama yerelleştirmesi şunları içerir:

1. Uygulamanın içeriğini yerelleştirilebilir yapın
1. Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın
1. Her istek için dil/kültür seçmek üzere bir strateji uygulayın

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Uygulamanın içeriğini yerelleştirilebilir yapın

<xref:Microsoft.Extensions.Localization.IStringLocalizer> ve <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> yerelleştirilmiş uygulamalar geliştirilirken üretkenliği artırmak için tasarlanmıştır. `IStringLocalizer`<xref:System.Resources.ResourceManager> <xref:System.Resources.ResourceReader> çalışma zamanında kültüre özgü kaynaklar sağlamak için ve kullanır. Arabirimin bir Dizin Oluşturucusu ve `IEnumerable` yerelleştirilmiş dizeleri döndürmesi için bir. `IStringLocalizer` , varsayılan dil dizelerinin bir kaynak dosyasında depolanmasını gerektirmez. Yerelleştirmeye yönelik bir uygulama geliştirebilir ve geliştirmede erken kaynak dosyaları oluşturmaya gerek yoktur. Aşağıdaki kod, yerelleştirme için "başlık hakkında" dizesinin nasıl sarılacağı gösterilmektedir.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [bağımlılık ekleme](dependency-injection.md)işleminden gelir. Yerelleştirilmiş "başlık hakkında" değeri bulunmazsa, Dizin Oluşturucu anahtarı döndürülür, diğer bir deyişle "başlık hakkında" dizesidir. Uygulama geliştirmeye odaklanabilmeniz için varsayılan dil değişmez dizelerini uygulamada bırakabilir ve Localizer ' da kaydırabilirsiniz. Uygulamanızı varsayılan diliniz ile geliştirin ve öncelikle varsayılan bir kaynak dosyası oluşturmadan yerelleştirme adımına hazırlayın. Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz. Birçok geliştirici için, varsayılan Language *. resx* dosyası olmayan yeni iş akışı ve yalnızca dize değişmez değerlerini sarmalama, bir uygulamayı yerelleştirme yükünü azaltabilir. Diğer geliştiriciler geleneksel iş akışını tercih eder, daha uzun dize sabit değerleri ile çalışmayı kolaylaştırır ve yerelleştirilmiş dizelerin güncelleştirilmesini kolaylaştırır.

`IHtmlLocalizer<T>`HTML içeren kaynaklar için uygulamayı kullanın. `IHtmlLocalizer` HTML, kaynak dizesinde biçimlendirilen, ancak kaynak dizenin kendisini kodlayan bağımsız değişkenleri kodluyor. Aşağıda vurgulanan örnekte, yalnızca `name` parametresinin DEĞERI HTML kodlamalı olur.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

En düşük düzeyde, `IStringLocalizerFactory` [bağımlılık ekleme](dependency-injection.md)işlemini alabilirsiniz:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Yukarıdaki kod, iki fabrika oluşturma yönteminin her birini gösterir.

Yerelleştirilmiş dizelerinizi denetleyiciye, alana veya yalnızca bir kapsayıcıya göre bölümleyebilirsiniz. Örnek uygulamada, paylaşılan kaynaklar için adlı bir kukla sınıf `SharedResource` kullanılır.

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

Bazı geliştiriciler, `Startup` genel veya paylaşılan dizeler içeren sınıfını kullanır. Aşağıdaki örnekte, `InfoController` ve `SharedResource` yerelleştiriciler kullanılır:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Yerelleştirmeyi görüntüle

`IViewLocalizer`Hizmet, bir [Görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeler sağlar. `ViewLocalizer`Sınıfı bu arabirimi uygular ve görünüm dosyası yolundan kaynak konumunu bulur. Aşağıdaki kod, öğesinin varsayılan uygulamasının nasıl kullanılacağını gösterir `IViewLocalizer` :

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Varsayılan uygulama, `IViewLocalizer` görünümün dosya adına göre kaynak dosyasını bulur. Genel paylaşılan kaynak dosyası kullanma seçeneği yoktur. `ViewLocalizer` , kullanarak yerelleştirici uygular `IHtmlLocalizer` , bu nedenle Razor YERELLEŞTIRILMIŞ dizeyi HTML olarak kodlamaz. Kaynak dizelerini parametreleştirebilirsiniz ve `IViewLocalizer` kaynak dizesini değil, PARAMETRELERI HTML olarak kodlayabilirsiniz. Aşağıdaki biçimlendirmeyi göz önünde bulundurun Razor :

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Bir Fransızca kaynak dosyası şunları içerebilir:

| Anahtar | Değer |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

İşlenmiş görünüm, kaynak dosyasındaki HTML işaretlemesini içerir.

> [!NOTE]
> Genellikle, HTML değil yalnızca metni yerelleştirin.

Bir görünümde paylaşılan kaynak dosyasını kullanmak için şunu ekleme `IHtmlLocalizer<T>` :

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Dataaçıklamaların yerelleştirilmesi

Dataek açıklamalar hata iletileri ile yereldir `IStringLocalizer<T>` . Seçeneğini kullanarak `ResourcesPath = "Resources"` , içindeki hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde depolanabilir:

* *Resources/Viewmodeller. account. RegisterViewModel. fr. resx*
* *Kaynaklar/Viewmodeller/hesap/RegisterViewModel. fr. resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

ASP.NET Core MVC 1.1.0 ve üzeri, doğrulama olmayan öznitelikler yereldir. ASP.NET Core MVC 1,0, doğrulama olmayan öznitelikler için yerelleştirilmiş **dizeleri aramaz.**

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Birden çok sınıf için bir kaynak dizesi kullanma

Aşağıdaki kod, birden çok sınıfa sahip doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Yukarıdaki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx öğesine karşılık gelen sınıftır. Bu yaklaşımda, veri açıklamaları yalnızca her bir `SharedResource` sınıf için kaynak yerine yalnızca kullanılır.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Destekettiğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın

### <a name="supportedcultures-and-supporteduicultures"></a>Supportedkültürleri ve SupportedUICultures

ASP.NET Core iki kültür değeri belirtmenize olanak tanır `SupportedCultures` `SupportedUICultures` . İçin [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` Tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler. `SupportedCultures` Ayrıca metnin, büyük/küçük harf kurallarının ve dize karşılaştırmalarının sıralama sırasını belirler. Sunucunun kültürü nasıl aldığı hakkında daha fazla bilgi için bkz [. CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) . , `SupportedUICultures` Hangi çevrilmiş dizelerin ( *. resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından arandığını belirler. `ResourceManager`Yalnızca tarafından belirlenen kültüre özgü dizeleri arar `CurrentUICulture` . .NET 'teki her iş parçacığında `CurrentCulture` ve `CurrentUICulture` nesneler bulunur. ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler. Örneğin, geçerli iş parçacığının kültürü "en-US" (Ingilizce, Birleşik Devletler) olarak ayarlandıysa, `DateTime.Now.ToLongDateString()` "Perşembe, 18 şubat 2016" görüntüler, ancak `CurrentCulture` "ES-es" (Ispanyolca, İspanya) olarak ayarlandıysa çıkış "Jueves, 18 de febrero de 2016" olacaktır.

## <a name="resource-files"></a>Kaynak dosyalar

Kaynak dosyası, koddan yerelleştirilebilir dizeleri ayırmak için kullanışlı bir mekanizmadır. Varsayılan olmayan dil için çevrilmiş dizeler *. resx* kaynak dosyalarında yalıtılmıştır. Örneğin, çevrilmiş dizeleri içeren *Welcome. es. resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz. "es", Ispanyolca için dil kodudur. Bu kaynak dosyasını Visual Studio 'da oluşturmak için:

1. **Çözüm Gezgini**' de, **Add** > **Yeni öğe**Ekle > kaynak dosyasını içerecek klasöre sağ tıklayın.

   ![İç içe bağlamsal bağlam menüsü: Çözüm Gezgini, kaynaklar için bir bağlamsal menü açıktır. İkinci bağlamsal menü, yeni öğe komutunun vurgulandığı ekleme için açıktır.](localization/_static/newi.png)

1. **Yüklü şablonları ara** kutusuna "kaynak" yazın ve dosyayı adlandırın.

   ![Yeni öğe Ekle iletişim kutusu](localization/_static/res.png)

1. **Ad** sütununa anahtar değerini (yerel dize) ve **değer** sütununda çevrilmiş dizeyi girin.

   ![Ad sütunundaki Merhaba. es. resx dosyası (Ispanyolca için hoş geldiniz kaynak dosyası) ve değer sütununda Hola (Ispanyolca) adlı kelime](localization/_static/hola.png)

   Visual Studio, *Welcome. es. resx* dosyasını gösterir.

   ![Hoş geldiniz Ispanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a>Kaynak dosyası adlandırma

Kaynaklar, sınıfının tam tür adı için derleme adı eksi olarak adlandırılır. Örneğin, ana derlemesi sınıf için olan bir projedeki bir Fransızca kaynak, `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` *Startup. fr. resx*olarak adlandırılır. Sınıf için bir kaynak `LocalizationWebsite.Web.Controllers.HomeController` *denetleyicileri. HomeController. fr. resx*olarak adlandırılır. Hedeflenen sınıfınızın ad alanı, derleme adı ile aynı değilse, tam tür adına ihtiyacınız olur. Örneğin, örnek projede, tür için bir kaynak `ExtraNamespace.Tools` *ExtraNamespace. Tools. fr. resx*olarak adlandırılır.

Örnek projede, `ConfigureServices` yöntemi `ResourcesPath` "resources" olarak ayarlıyor, bu nedenle ana denetleyicinin Fransızca kaynak dosyasının proje göreli yolu *Resources/Controllers. HomeController. fr. resx*olur. Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri de kullanabilirsiniz. Ana denetleyici için yol *kaynaklar/denetleyiciler/HomeController. fr. resx*olacaktır. `ResourcesPath`Seçeneğini kullanmazsanız, *. resx* dosyası proje temel dizinine gidecek. İçin kaynak dosyası, `HomeController` *Controllers. HomeController. fr. resx*olarak adlandırılır. Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.

| Kaynak adı | Nokta veya yol adlandırma |
| ------------   | ------------- |
| Kaynaklar/denetleyiciler. HomeController. fr. resx | Nokta  |
| Kaynaklar/denetleyiciler/HomeController. fr. resx  | Yol |

Görünümlerde kullanılan kaynak `@inject IViewLocalizer` dosyaları Razor benzer bir model izler. Bir görünüm için kaynak dosyası, nokta adlandırması veya yol adlandırması kullanılarak adlandırılabilir. Razor kaynak dosyalarını görüntüleme, ilişkili görünüm dosyalarının yolunu taklit eden. `ResourcesPath`"Resources" olarak belirlediğimiz varsayılarak, *Görünümler/Home/about. cshtml* görünümü ile ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:

* Kaynaklar/görünümler/giriş/about. fr. resx

* Kaynaklar/görünümler. Home. about. fr. resx

`ResourcesPath`Seçeneğini kullanmazsanız, bir görünüm için *. resx* dosyası görünümüyle aynı klasörde bulunur.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar. 

> [!WARNING]
> Bu durum, projenin adı geçerli bir .NET tanımlayıcısı olmadığında ortaya çıkabilir. Örneğin, `my-project-name.csproj` `my_project_name` Bu hata için kök ad alanını ve derleme adını kullanır `my-project-name` . 

Bir derlemenin kök ad alanı, derleme adından farklıysa:

* Yerelleştirme varsayılan olarak çalışmaz.
* Yerelleştirme, derleme içinde kaynakların aranacağı yol nedeniyle başarısız olur. `RootNamespace` , yürütme işlemi için kullanılamayan bir derleme zamanı değeridir. 

, `RootNamespace` Öğesinden farklıysa, `AssemblyName` *AssemblyInfo.cs* içine aşağıdakini ekleyin (parametre değerleri gerçek değerlerle değiştirilmelidir):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Yukarıdaki kod resx dosyalarının başarıyla çözümlenmesine izin vermez.

## <a name="culture-fallback-behavior"></a>Kültür geri dönüş davranışı

Bir kaynak aranırken, yerelleştirme "kültür geri dönüş" bölümünde ilgilenir. İstenen kültürden başlayarak, bulunamazsa bu kültürün üst kültürüne geri döner. Bir kenara de, [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder. Bu genellikle (her zaman değil), National signifier 'in ISO 'dan kaldırılması anlamına gelir. Örneğin, Meksika 'da konuşulan Ispanyolca diyalekt "es-MX" dir. Ana "es" &mdash; İspanyolca herhangi bir ülkeye özgü değildir.

Sitenizin "fr-CA" kültürünü kullanarak "hoş geldiniz" kaynağı için bir istek aldığını düşünün. Yerelleştirme sistemi aşağıdaki kaynakları sırayla arar ve ilk eşleşmeyi seçer:

* *Welcome.fr-CA. resx*
* *Welcome. fr. resx*
* *Welcome. resx* ( `NeutralResourcesLanguage` "fr-CA" ise)

Örnek olarak, ". fr" kültür göstergesini kaldırırsanız ve kültürü Fransızca olarak ayarlarsanız, varsayılan kaynak dosyası okunurdur ve dizeler yerelleştirilir. Kaynak Yöneticisi, istenen kültürü hiçbir şey karşılamıyorsa, için bir varsayılan veya geri dönüş kaynağı belirler. Yalnızca istenen kültür için bir kaynak eksik olduğunda anahtarı döndürmek istiyorsanız varsayılan bir kaynak dosyanız olmamalıdır.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio ile kaynak dosyaları oluşturma

Dosya adında bir kültür olmadan Visual Studio 'da bir kaynak dosyası oluşturursanız (örneğin, *Welcome. resx*), Visual Studio her bir dize için bir özelliği olan bir C# sınıfı oluşturur. ASP.NET Core, genellikle istediğiniz gibi değildir. Genellikle Default *. resx* kaynak dosyanız (kültür adı olmayan bir *. resx* dosyası) yoktur. *. Resx* dosyasını bir kültür adı (örneğin, *Welcome. fr. resx*) ile oluşturmanızı öneririz. Kültür adı ile bir *. resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyası oluşturmaz.

### <a name="add-other-cultures"></a>Diğer kültürleri Ekle

Her dil ve kültür bileşimi (varsayılan dil dışında), benzersiz bir kaynak dosyası gerektirir. ISO dili kodlarının dosya adının parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel ayarlar için kaynak dosyaları oluşturun (örneğin, **en-US**, **fr-CA**ve **en-GB**). Bu ISO kodları, *Welcome.es-MX. resx* (Ispanyolca/Meksika) içinde olduğu gibi dosya adı ve *. resx* dosya uzantısı arasına yerleştirilir.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Her istek için dil/kültür seçmek üzere bir strateji uygulayın

### <a name="configure-localization"></a>Yerelleştirmeyi yapılandırma

Yerelleştirme, `Startup.ConfigureServices` yönteminde yapılandırılır:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` Yerelleştirme hizmetlerini hizmetler kapsayıcısına ekler. Yukarıdaki kod, kaynakların yolunu da "resources" olarak ayarlar.

* `AddViewLocalization` yerelleştirilmiş görünüm dosyaları için destek ekler. Bu örnek görünümde yerelleştirme, görünüm dosyası sonekini temel alır. Örneğin, *Index. fr. cshtml* dosyasındaki "fr".

* `AddDataAnnotationsLocalization``DataAnnotations`soyutlamalar aracılığıyla yerelleştirilmiş doğrulama iletileri için destek ekler `IStringLocalizer` .

### <a name="localization-middleware"></a>Yerelleştirme ara yazılımı

Bir istekteki geçerli kültür, yerelleştirme [Ara](xref:fundamentals/middleware/index)ortamında ayarlanır. Yerelleştirme ara yazılımı `Startup.Configure` yönteminde etkindir. Yerelleştirme ara yazılımı, istek kültürünü denetlemeyebilir (örneğin,) herhangi bir ara yazılım önce yapılandırılmalıdır `app.UseMvcWithDefaultRoute()` .

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` bir `RequestLocalizationOptions` nesnesi başlatır. Her istekte içindeki listesinin listesi `RequestCultureProvider` `RequestLocalizationOptions` numaralandırılır ve istek kültürünü başarıyla belirleyebilmesi için ilk sağlayıcı kullanılır. Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Varsayılan liste, en çok belirli olan en az özel. Makalenin ilerleyen kısımlarında, sırayı nasıl değiştirekullanabileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğiniz hakkında bilgi edineceksiniz. Sağlayıcıların hiçbiri istek kültürünü belirleyeiyorsa, `DefaultRequestCulture` kullanılır.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Bazı uygulamalar, ayarlamak için bir sorgu dizesi kullanır <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> . cookieVeya Accept-Language üstbilgi yaklaşımını kullanan uygulamalar için, URL 'ye bir sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır. Varsayılan olarak,, `QueryStringRequestCultureProvider` listede ilk yerelleştirme sağlayıcısı olarak kaydedilir `RequestCultureProvider` . Sorgu dizesi parametrelerini ve öğesini geçirirsiniz `culture` `ui-culture` . Aşağıdaki örnek, belirli kültürü (dil ve bölge) Ispanyolca/Meksika olarak ayarlar:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Yalnızca iki ( `culture` veya) birini geçirirseniz `ui-culture` , sorgu dizesi sağlayıcısı, her iki değeri de geçirdiğiniz birini kullanarak ayarlar. Örneğin, yalnızca kültür ayarlandığında, ve ' nin her ikisi de ayarlanır `Culture` `UICulture` :

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Üretim uygulamaları genellikle ASP.NET Core kültür ile kültürü ayarlamak için bir mekanizma sağlar cookie . `MakeCookieValue`Oluşturmak için yöntemini kullanın cookie .

, `CookieRequestCultureProvider` `DefaultCookieName` cookie Kullanıcının tercih ettiği kültür bilgilerini izlemek için kullanılan varsayılan adı döndürür. Varsayılan cookie ad `.AspNetCore.Culture` .

cookieBiçimi `c=%LANGCODE%|uic=%LANGCODE%` , ve nerede olduğu `c` , `Culture` `uic` `UICulture` Örneğin:

```
c=en-UK|uic=en-US
```

Kültür bilgisi ve UI kültürünün yalnızca birini belirtirseniz, belirtilen kültür hem kültür bilgileri hem de UI kültürü için kullanılacaktır.

### <a name="the-accept-language-http-header"></a>Accept-Language HTTP üstbilgisi

[Accept-Language üst bilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) tarayıcıların çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmeye yöneliktir. Bu ayar, tarayıcının temel alınan işletim sisteminden gönderme veya devralma olarak ayarlandığını gösterir. Bir tarayıcıdan Accept-Language HTTP üst bilgisi, kullanıcının tercih ettiği dili algılamaya yönelik uygun olmayan bir yoldur (bkz. [bir tarayıcıda dil tercihlerini ayarlama](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). Bir üretim uygulaması, kullanıcının kültür seçimini özelleştirmenin bir yolunu içermelidir.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE 'de Accept-Language HTTP üstbilgisini ayarlama

1. Dişli simgesinden **Internet seçenekleri**' ne dokunun.

1. **Diller**' e dokunun.

   ![Internet seçenekleri](localization/_static/lang.png)

1. **Dil tercihlerini ayarla**' ya dokunun.

1. **Dil ekle**' ye dokunun.

1. Dilini ekleyin.

1. Dile dokunun ve ardından **Yukarı taşı**' ya dokunun.

### <a name="the-content-language-http-header"></a>Content-Language HTTP üst bilgisi

[Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) varlık üst bilgisi:

* , Hedef kitleleri için tasarlanan dilleri tanımlamakta kullanılır.
* Kullanıcının kendi tercih ettiği dile göre ayırt etmesine izin verir.

Varlık üstbilgileri hem HTTP isteklerinde hem de yanıtlarda kullanılır.

`Content-Language`Üst bilgi, özelliği ayarlanarak eklenebilir `ApplyCurrentCultureToResponseHeaders` .

`Content-Language`Üst bilgi ekleniyor:

* Requestlocalizationara yazılımı ile üstbilgiyi ayarlamaya izin verir `Content-Language` `CurrentUICulture` .
* Yanıt üst bilgisini açıkça ayarlama gereksinimini ortadan kaldırır `Content-Language` .

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Özel bir sağlayıcı kullan

Müşterilerinizin kendi dil ve kültürünü veritabanlarınızı veritabanlarına depolamasına izin vermek istediğinizi varsayalım. Kullanıcı için bu değerleri aramak üzere bir sağlayıcı yazabilirsiniz. Aşağıdaki kod, özel bir sağlayıcının nasıl ekleneceğini göstermektedir:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

`RequestLocalizationOptions`Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın.

### <a name="set-the-culture-programmatically"></a>Kültürü program aracılığıyla ayarlama

[GitHub](https://github.com/aspnet/entropy) 'daki Bu örnek **Yerelleştirme. starterweb** projesi, ayarlamak için Kullanıcı arabirimi içerir `Culture` . *Views/Shared/_SelectLanguagePartial. cshtml* dosyası desteklenen kültürler listesinden kültürü seçmenize olanak sağlar:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Görünümler/Shared/_SelectLanguagePartial. cshtml* dosyası, `footer` Düzen dosyasının bölümüne eklenir, bu nedenle tüm görünümlerde kullanılabilir hale gelir:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

`SetLanguage`Yöntemi kültürü ayarlar cookie .

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Bu proje için örnek koda *_SelectLanguagePartial. cshtml* 'yi ekleyemezsiniz. [GitHub](https://github.com/aspnet/entropy) 'daki **Yerelleştirme. starterweb** projesi, `RequestLocalizationOptions` Razor [bağımlılık ekleme](dependency-injection.md) kapsayıcısına kısmi bir şekilde akışı için kod içerir.

## <a name="model-binding-route-data-and-query-strings"></a>Model bağlama yolu verileri ve sorgu dizeleri

[Model bağlama yolu verilerinin ve sorgu dizelerinin Genelleştirme davranışını](xref:mvc/models/model-binding#glob)inceleyin.

## <a name="globalization-and-localization-terms"></a>Genelleştirme ve yerelleştirme koşulları

Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan ilgili karakter kümelerinin temel olarak anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını gerektirir. Tüm bilgisayarlar metni sayı (kodlar) olarak depolayabilse de, farklı sistemler farklı sayılar kullanarak aynı metni depolar. Yerelleştirme süreci, belirli bir kültür/yerel ayar için uygulama kullanıcı arabirimini (UI) çevirmeye başvurur.

[Yerelleştirilebilirlik](/dotnet/standard/globalization-localization/localizability-review) , bir Genelleştirilmiş uygulamasının yerelleştirme için hazırlandığının doğrulanması için bir ara işlemdir.

Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) biçimi `<languagecode2>-<country/regioncode2>` , burada `<languagecode2>` dil kodudur ve `<country/regioncode2>` alt kültür kodudur. Örneğin, `es-CL` İspanyolca (Şili) için, `en-US` ingilizce (Birleşik Devletler) ve `en-AU` İngilizce (Avustralya) için. [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) , bir dille ILIŞKILI bir ISO 639 2-Letter küçük harfli kültür kodu ve bir ülke veya bölgeyle ILIŞKILI bir ISO 3166 2 harfli büyük harf alt kültür kodu birleşimidir. Bkz. <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Uluslararası duruma getirme genellikle "I18N" olarak kısaltılır. Kısaltma ilk ve son harfleri ve aralarındaki harflerin sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harflerin sayısını temsil eder. Aynı Genelleştirme (G11N) ve yerelleştirme (L10N) için de geçerlidir.

Larındaki

* Genelleştirme (G11N): bir uygulamanın farklı dil ve bölgeleri desteklemesini sağlama işlemi.
* Yerelleştirme (L10N): belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.
* Uluslararası duruma getirme (I18N): hem Genelleştirme hem de yerelleştirmeyi açıklar.
* Kültür: bir dildir ve isteğe bağlı olarak bir bölgedir.
* Nötr kültür: belirtilen dile sahip, ancak bölge olmayan bir kültür. (örneğin, "en", "es")
* Belirli kültür: belirtilen dile ve bölgeye sahip bir kültür. (örneğin, "en-US", "en-GB", "es-CL")
* Üst kültür: belirli bir kültürü içeren nötr kültür. (örneğin, "tr", "en-US" ve "en-GB" öğesinin üst kültürüdür)
* Yerel ayar: bir yerel ayar kültür ile aynıdır.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* Makalesinde kullanılan [Yerelleştirme. StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) .
* [.NET uygulamalarını genelleştirmek ve yerelleştirme](/dotnet/standard/globalization-localization/index)
* [.resx Dosyalarındaki Kaynaklar](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft çok dilli uygulama araç seti](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Yerelleştirme & genel türler](http://hishambinateya.com/localization-and-generics)

::: moniker-end
