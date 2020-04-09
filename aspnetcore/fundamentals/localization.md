---
title: ASP.NET Çekirdek'te küreselleşme ve yerelleşme
author: rick-anderson
description: ASP.NET Core'un içeriği farklı dillerde ve kültürlerde yerelleştirmek için nasıl hizmet ve ara yazılım sağladığını öğrenin.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: b175354220a8a71c029e005f27443d5a72749a11
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662122"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>ASP.NET Çekirdek'te küreselleşme ve yerelleşme

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), ve [Hisham Bin Ateya](https://twitter.com/hishambinateya)

ASP.NET Core ile çok dilli bir web sitesi oluşturmak, sitenizin daha geniş bir kitleye ulaşmasını sağlayacaktır. ASP.NET Core, farklı dillerde ve kültürlere yerelleştirme hizmetleri ve ara yazılımlar sunmaktadır.

Uluslararasılaşma [Küreselleşme](/dotnet/api/system.globalization) ve [Yerelleşme](/dotnet/standard/globalization-localization/localization)içerir. Küreselleşme, farklı kültürleri destekleyen uygulamalar tasarlama işlemidir. Genelleştirme, belirli coğrafi bölgelerle ilgili tanımlı bir dil komut dosyası kümesinin girişi, görüntülenmesi ve çıktısı için destek ekler.

Yerelleştirme, yerelleştirilebilirlik için zaten işlemiş olduğunuz küreselleştirilmiş bir uygulamayı belirli bir kültüre/yerelliğe uyarlama işlemidir. Daha fazla bilgi için bu belgenin sonuna yakın **Küreselleşme ve yerelleştirme terimlerini** görün.

Uygulama yerelleştirme aşağıdakileri içerir:

1. Uygulamanın içeriğini yerelleştirilebilir hale getirin

2. Desteklediğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın

3. Her istek için dili/kültürü seçmek için bir strateji uygulayın

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Uygulamanın içeriğini yerelleştirilebilir hale getirin

ASP.NET Core'da `IStringLocalizer` tanıtıldı `IStringLocalizer<T>` ve yerelleştirilmiş uygulamalar geliştirirken üretkenliği artırmak için geliştirildi. `IStringLocalizer`çalışma zamanında kültüre özgü kaynaklar sağlamak için [ResourceManager](/dotnet/api/system.resources.resourcemanager) ve [ResourceReader'ı](/dotnet/api/system.resources.resourcereader) kullanır. Basit arabirimde bir dizinleyici ve yerelleştirilmiş dizeleri döndüren için bir `IEnumerable` vardır. `IStringLocalizer`varsayılan dil dizelerini bir kaynak dosyasında depolamanızı gerektirmez. Yerelleştirme için hedeflenen bir uygulama geliştirebilirsiniz ve geliştirme nin başında kaynak dosyaları oluşturmanız gerekmez. Aşağıdaki kod, yerelleştirme için "Başlık Hakkında" dizesini nasıl sarın gösterir.

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

Yukarıdaki kodda, `IStringLocalizer<T>` uygulama [Bağımlılık Enjeksiyon](dependency-injection.md)geliyor. "Başlık Hakkında"nın yerelleştirilmiş değeri bulunamazsa, dizinleyici anahtarı döndürülür, yani "Başlık Hakkında" dizesi. Uygulamayı geliştirmeye odaklanabilmek için varsayılan dil deki edebi dizeleri uygulamada bırakabilir ve yerelleştiriciye sarabilirsiniz. Varsayılan dilinizle uygulamanızı geliştirir ve varsayılan kaynak dosyası oluşturmadan yerelleştirme adımına hazırlarsınız. Alternatif olarak, geleneksel yaklaşımı kullanabilir ve varsayılan dil dizesini almak için bir anahtar sağlayabilirsiniz. Birçok geliştirici için varsayılan bir dile sahip olmamak *.resx* dosyası ve yalnızca dize literallerini sarmak, bir uygulamayı yerelleştirmenin ek yüküne neden olabilir. Diğer geliştiriciler, daha uzun dize gerçekleriyle çalışmayı ve yerelleştirilmiş dizeleri güncelleştirmeyi kolaylaştırabileceğinden geleneksel iş akışını tercih eder.

`IHtmlLocalizer<T>` HTML içeren kaynaklar için uygulamayı kullanın. `IHtmlLocalizer`HTML, kaynak dizesinde biçimlendirilmiş bağımsız değişkenleri kodlar, ancak KAYNAK dizesini HTML kodlamaz. Aşağıda vurgulanan örnekte, yalnızca `name` parametrenin değeri HTML kodlanır.

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

**Not:** Genellikle html değil, yalnızca metni yerelleştirmek istersiniz.

En düşük düzeyde, Bağımlılık `IStringLocalizerFactory` [Enjeksiyon](dependency-injection.md)ulabilirsiniz:

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Yukarıdaki kod, her iki fabrika oluşturma yöntemlerini gösterir.

Yerelleştirilmiş dizelerinizi denetleyiciye, alana göre bölümlere alabilir veya yalnızca bir kapsayıcınız olabilir. Örnek uygulamada, paylaşılan kaynaklar için `SharedResource` adlandırılmış bir sahte sınıf kullanılır.

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

Bazı geliştiriciler `Startup` sınıfı genel veya paylaşılan dizeleri içerecek şekilde kullanır. Aşağıdaki örnekte, `InfoController` yerelleştiriciler ve `SharedResource` yerelleştiriciler kullanılır:

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Yerelleştirmeyi görüntüleme

Hizmet, `IViewLocalizer` [görünüm](xref:mvc/views/overview)için yerelleştirilmiş dizeleri sağlar. Sınıf `ViewLocalizer` bu arabirimi uygular ve kaynak konumunu görünüm dosyası yolundan bulur. Aşağıdaki kod, varsayılan uygulamanın nasıl `IViewLocalizer`kullanılacağını gösterir:

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

Varsayılan uygulama, `IViewLocalizer` görünümün dosya adını temel alan kaynak dosyasını bulur. Genel paylaşılan kaynak dosyasını kullanma seçeneği yoktur. `ViewLocalizer`kullanarak yerelleştiriciyi `IHtmlLocalizer`uygular, böylece Razor yerelleştirilmiş dizeyi HTML kodlamaz. Kaynak dizeleri parametrenize `IViewLocalizer` edebilirsiniz ve HTML parametreleri kodlar, ancak kaynak dizesini kodlar. Aşağıdaki Jilet biçimlendirmesini göz önünde bulundurun:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Bir Fransızca kaynak dosyası aşağıdakileri içerebilir:

| Anahtar | Değer |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

İşlenen görünüm, kaynak dosyasındaki HTML biçimlendirmesini içerir.

**Not:** Genellikle html değil, yalnızca metni yerelleştirmek istersiniz.

Paylaşılan kaynak dosyasını görünümde kullanmak `IHtmlLocalizer<T>`için şunları enjekte edin:

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>DataAnnotations yerelleştirme

DataAnnotations hata iletileri ile `IStringLocalizer<T>`yerelleştirilir. `ResourcesPath = "Resources"`Seçeneğini kullanarak, hata iletileri `RegisterViewModel` aşağıdaki yollardan birinde saklanabilir:

* *Kaynaklar/ViewModels.Account.RegisterViewModel.fr.resx*
* *Kaynaklar/Görünüm Modelleri/Hesap/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

Core MVC 1.1.0 ve daha yüksek ASP.NET, doğrulama olmayan öznitelikler yerelleştirilmiştir. ASP.NET Core MVC 1.0, doğrulama olmayan öznitelikler için yerelleştirilmiş dizeleri **aramaz.**

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Birden çok sınıf için tek kaynak dizesini kullanma

Aşağıdaki kod, birden çok sınıfiçeren doğrulama öznitelikleri için bir kaynak dizesinin nasıl kullanılacağını gösterir:

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

Önceki kodda, `SharedResource` doğrulama iletilerinizin depolandığı resx'e karşılık gelen sınıf dır. Bu yaklaşımla, DataAnnotations `SharedResource`yalnızca , yerine her sınıf için kaynak kullanır.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Desteklediğiniz diller ve kültürler için yerelleştirilmiş kaynaklar sağlayın

### <a name="supportedcultures-and-supporteduicultures"></a>Desteklenen Kültürler ve DesteklenenUICultures

ASP.NET Core iki kültür değeri `SupportedCultures` belirtmenizi sağlar ve `SupportedUICultures`. [CultureInfo](/dotnet/api/system.globalization.cultureinfo) nesnesi, `SupportedCultures` tarih, saat, sayı ve para birimi biçimlendirme gibi kültüre bağımlı işlevlerin sonuçlarını belirler. `SupportedCultures`ayrıca metnin sıralama sırasını, kasa kurallarını ve dize karşılaştırmalarını belirler. Sunucunun Kültürü nasıl aldığı hakkında daha fazla bilgi için [CultureInfo.CurrentCulture'a](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) bakın. Dizeleri çeviren `SupportedUICultures` belirlemeler *(.resx* dosyalarından) [ResourceManager](/dotnet/api/system.resources.resourcemanager)tarafından aranır. Basitçe, `ResourceManager` kültüre özgü dizeleri `CurrentUICulture`arar. .NET'teki her `CurrentCulture` `CurrentUICulture` iş parçacığı ve nesne vardır. ASP.NET Core kültüre bağımlı işlevleri işlerken bu değerleri inceler. Örneğin, geçerli iş parçacığının kültürü "en-US" (İngilizce, AMERIKA `DateTime.Now.ToLongDateString()` Birleşik Devletleri) olarak ayarlanmışsa, "18 Şubat `CurrentCulture` 2016 Perşembe" görüntülenirse, ancak "es-ES" (İspanyolca, İspanya) olarak ayarlanırsa çıktı "jueves, 18 de febrero de 2016" olacaktır.

## <a name="resource-files"></a>Kaynak dosyalar

Kaynak dosyası, yerelleştirilebilir dizeleri koddan ayırmak için yararlı bir mekanizmadır. Varsayılan olmayan dil için çevrilmiş dizeleri yalıtılmış *.resx* kaynak dosyaları. Örneğin, çevrilmiş dizeleri içeren *Welcome.es.resx* adlı İspanyolca kaynak dosyası oluşturmak isteyebilirsiniz. "es" İspanyolca'nın dil kodudur. Visual Studio'da bu kaynak dosyasını oluşturmak için:

1. **Çözüm Gezgini'nde,** Kaynak dosyasını içerecek klasöre sağ tıklayın >**Yeni Öğe** **Ekle.** > 

    ![İç içe bağlamsal menü: Solution Explorer'da Kaynaklar için bağlamsal bir menü açıktır. Vurgulanan Yeni Öğe komutunu gösteren Ekle için ikinci bir bağlamsal menü açılır.](localization/_static/newi.png)

2. Arama **yüklü şablonlar** kutusuna "kaynak" girin ve dosyayı adlandırın.

    ![Yeni Öğe iletişim kutusu ekle](localization/_static/res.png)

3. **Ad** sütununa anahtar değerini (yerel dizeyi) ve **Değer** sütununa çevrilmiş dizeyi girin.

    ![Welcome.es.resx dosyası (İspanyolca için hoş geldiniz kaynak dosyası) Ad sütununda Merhaba sözcüğü ve Değer sütununda Hola (İspanyolca Merhaba) sözcüğü ile](localization/_static/hola.png)

    Visual Studio *Welcome.es.resx* dosyasını gösterir.

    ![Hoş Geldiniz İspanyolca (es) kaynak dosyasını gösteren Çözüm Gezgini](localization/_static/se.png)

## <a name="resource-file-naming"></a>Kaynak dosyası adlandırma

Kaynaklar, sınıflarının tam tür adı eksi derleme adı için adlandırılır. Örneğin, ana derlemesi sınıf `LocalizationWebsite.Web.dll` `LocalizationWebsite.Web.Startup` için olan bir projedeki Bir Fransızca kaynak *Başlangıç.fr.resx*olarak adlandırılır. Sınıf `LocalizationWebsite.Web.Controllers.HomeController` için bir kaynak *Controllers.HomeController.fr.resx*adlı olacaktır. Hedeflediğiniz sınıfın ad alanı derleme adı ile aynı değilse, tam tür adı gerekir. Örneğin, örnek projede türü `ExtraNamespace.Tools` için bir kaynak *ExtraNamespace.Tools.fr.resx*adlı olacaktır.

Örnek projede, `ConfigureServices` yöntem "Kaynaklar" `ResourcesPath` olarak ayarlar, bu nedenle ev denetleyicisinin Fransızca kaynak dosyasıiçin proje göreli yolu *Kaynaklar/Controllers.HomeController.fr.resx'tir.* Alternatif olarak, kaynak dosyalarını düzenlemek için klasörleri kullanabilirsiniz. Ev denetleyicisi için yol *Kaynaklar/Denetleyiciler/HomeController.fr.resx*olacaktır. Seçeneği kullanmazsanız, `ResourcesPath` *.resx* dosyası proje temel dizinine gider. Kaynak `HomeController` dosyası *controllers.HomeController.fr.resx*adlı olacaktır. Nokta veya yol adlandırma kuralını kullanma seçeneği, kaynak dosyalarınızı nasıl düzenlemek istediğinize bağlıdır.

| Kaynak adı | Nokta veya yol adlandırma |
| ------------   | ------------- |
| Kaynaklar/Denetleyiciler.HomeController.fr.resx | Nokta  |
| Kaynaklar/Denetleyiciler/HomeController.fr.resx  | Yol |
|    |     |

Razor görünümlerinde kullanan `@inject IViewLocalizer` kaynak dosyaları da benzer bir desen izler. Görünüm için kaynak dosyası nokta adlandırma veya yol adlandırma kullanarak adlandırılabilir. Jilet görünümü kaynak dosyaları ilişkili görünüm dosyalarının yolunu taklit. "Kaynaklar" `ResourcesPath` olarak ayarladiğimizı varsayarsak, *Görünümler/Ana Sayfa/About.cshtml* görünümüyle ilişkili Fransızca kaynak dosyası aşağıdakilerden biri olabilir:

* Kaynaklar/Görünümler/Ana Sayfa/About.fr.resx

* Kaynaklar/Görünümler.Home.About.fr.resx

Seçeneği kullanmazsanız, `ResourcesPath` görünüm için *.resx* dosyası görünümle aynı klasörde bulunur.

### <a name="rootnamespaceattribute"></a>Rootnamespaceattribute 

[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) özniteliği, bir derlemenin kök ad alanı derleme adından farklı olduğunda bir derlemenin kök ad alanını sağlar. 

> [!WARNING]
> Bu, bir projenin adı geçerli bir .NET tanımlayıcısı olmadığında oluşabilir. Örneğin, `my-project-name.csproj` kök ad alanını `my_project_name` ve bu `my-project-name` hataya yol açan derleme adını kullanır. 

Bir derlemenin kök ad alanı derleme adından farklıysa:

* Yerelleştirme varsayılan olarak çalışmıyor.
* Yerelleştirme, derleme içinde kaynakların aranması nedeniyle başarısız olur. `RootNamespace`yürütme işlemi için kullanılamayan bir yapı zamanı değeridir. 

Farklı `RootNamespace` `AssemblyName`ise, *AssemblyInfo.cs* (parametre değerleri gerçek değerlerle değiştirilir) aşağıdakileri içerir:

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Önceki kod, resx dosyalarının başarılı bir şekilde çözümlemesini sağlar.

## <a name="culture-fallback-behavior"></a>Kültür geri dönüş davranışı

Bir kaynak ararken, yerelleştirme "kültür geri dönüş" meşgul. İstenen kültürden başlayarak, bulunamazsa, o kültürün ana kültürüne geri döner. Bir kenara, [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) özelliği üst kültürü temsil eder. Bu genellikle (ama her zaman değil) ISO gelen ulusal signifier kaldırılması anlamına gelir. Örneğin, Meksika'da konuşulan İspanyolca lehçesi "es-MX". Bu ebeveyn "es"&mdash;İspanyolca olmayan herhangi bir ülkeye özgü vardır.

Sitenizin "fr-CA" kültürünü kullanarak bir "Hoş Geldiniz" kaynağı için bir istek aldığını düşünün. Yerelleştirme sistemi sırayla aşağıdaki kaynakları arar ve ilk eşleşmeyi seçer:

* *Welcome.fr-CA.resx*
* *Hoşgeldiniz.fr.resx*
* *Welcome.resx* (eğer `NeutralResourcesLanguage` "fr-CA")

Örnek olarak, ".fr" kültür atası kaldırırsanız ve kültür Fransızca olarak ayarlanmışsa, varsayılan kaynak dosyası okunur ve dizeleri yerelleştirilmiştir. Kaynak yöneticisi, hiçbir şey istediğiniz kültürünüzü karşılamadığında varsayılan veya geri dönüş kaynağı belirler. İstenen kültür için bir kaynak eksikken anahtarı döndürmek istiyorsanız, varsayılan bir kaynak dosyanız olmamalıdır.

### <a name="generate-resource-files-with-visual-studio"></a>Visual Studio ile kaynak dosyaları oluşturma

Visual Studio'da dosya adında kültürü olmayan bir kaynak dosyası oluşturursanız (örneğin, *Welcome.resx),* Visual Studio her dize için bir özelliği olan bir C# sınıfı oluşturur. ASP.NET Core'da genelde böyle olmasını istemezsin. Genellikle varsayılan *.resx* kaynak dosyanız (kültür adı olmayan bir *.resx* dosyası) yoktur. *.resx* dosyasını bir kültür adı içeren oluşturmanızı öneririz (örneğin *Welcome.fr.resx).* Bir kültür *adındabir .resx* dosyası oluşturduğunuzda, Visual Studio sınıf dosyasını oluşturmaz.

### <a name="add-other-cultures"></a>Diğer kültürleri ekleme

Her dil ve kültür birleşimi (varsayılan dil dışında) benzersiz bir kaynak dosyası gerektirir. ISO dil kodlarının dosya adının bir parçası olduğu yeni kaynak dosyaları oluşturarak farklı kültürler ve yerel dosyalar için kaynak dosyaları oluşturursunuz (örneğin, **en-us**, **fr-ca**, ve **en-gb).** Bu ISO *kodları, Welcome.es-MX.resx* (İspanyolca/Meksika) gibi dosya adı ve *.resx* dosya uzantısı arasına yerleştirilir.

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Her istek için dili/kültürü seçmek için bir strateji uygulayın

### <a name="configure-localization"></a>Yerelleştirmeyi yapılandırma

Yerelleştirme `Startup.ConfigureServices` yönteminde yapılandırılır:

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* `AddLocalization`Yerelleştirme hizmetlerini hizmet kapsayıcısına ekler. Yukarıdaki kod da "Kaynaklar" için kaynak yolunu ayarlar.

* `AddViewLocalization`Yerelleştirilmiş görünüm dosyaları için destek ekler. Bu örnek görünümünde yerelleştirme görünüm dosyası sonekine dayanır. Örneğin *Index.fr.cshtml* dosyasındaki "fr"

* `AddDataAnnotationsLocalization`Soyutlamalar aracılığıyla `IStringLocalizer` `DataAnnotations` yerelleştirilmiş doğrulama iletileri için destek ekler.

### <a name="localization-middleware"></a>Yerelleştirme ara ware

Bir istekteki geçerli kültür, yerelleştirme [Middleware'inde](xref:fundamentals/middleware/index)ayarlanır. `Startup.Configure` Yöntemde yerelleştirme ara ware etkindir. Yerelleştirme ara ware istek kültürünü kontrol edebilir herhangi bir ara yazılım `app.UseMvcWithDefaultRoute()`önce yapılandırılmalıdır (örneğin, ).

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization`bir `RequestLocalizationOptions` nesneyi başharfe alar. Her `RequestCultureProvider` istekte `RequestLocalizationOptions` liste numaralandırılır ve istek kültürünü başarıyla belirleyebilen ilk sağlayıcı kullanılır. Varsayılan sağlayıcılar `RequestLocalizationOptions` sınıftan gelir:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Varsayılan liste en çok belirli den en az özel gider. Makalenin ilerleyen saatlerinde, siparişi nasıl değiştirebileceğinizi ve hatta özel bir kültür sağlayıcısı nasıl ekleyebileceğinizi göreceğiz. Sağlayıcılardan hiçbiri istek kültürünü belirleyemezse, bu `DefaultRequestCulture` durum kullanılır.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Bazı uygulamalar [kültür ve ui kültürünü](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)ayarlamak için bir sorgu dizesi kullanır. Çerez veya Kabul Dili üstbilgisi yaklaşımını kullanan uygulamalar için, URL'ye sorgu dizesi eklemek hata ayıklama ve test kodu için yararlıdır. Varsayılan olarak, `QueryStringRequestCultureProvider` `RequestCultureProvider` listedeki ilk yerelleştirme sağlayıcısı olarak kaydedilir. Sorgu dize parametrelerini `culture` ve `ui-culture`. Aşağıdaki örnek, Belirli bir kültürü (dil ve bölge) İspanyolca/Meksika olarak belirlemektedir:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Yalnızca iki (veya),`culture` `ui-culture`sorgu dize sağlayıcısından birini geçerseniz, geçiş yaptığınız değeri kullanarak her iki değeri de ayarlar. Örneğin, sadece kültür ayarı hem `Culture` ve `UICulture`hem de ayarlar:

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a>CookieRequestCultureProvider

Üretim uygulamaları genellikle ASP.NET Çekirdek kültür çerezi ile kültürü ayarlamak için bir mekanizma sağlar. Bir `MakeCookieValue` çerez oluşturmak için yöntemi kullanın.

Kullanıcının `CookieRequestCultureProvider` `DefaultCookieName` tercih edilen kültür bilgilerini izlemek için kullanılan varsayılan çerez adını döndürür. Varsayılan çerez adı. `.AspNetCore.Culture`

Çerez biçimi `c=%LANGCODE%|uic=%LANGCODE%`, `c` nerede `Culture` `uic` ve, `UICulture`örneğin:

    c=en-UK|uic=en-US

Yalnızca kültür bilgileri ve Kullanıcı Arabirimi kültüründen birini belirtirseniz, belirtilen kültür hem kültür bilgisi hem de Kullanıcı Arabirimi kültürü için kullanılır.

### <a name="the-accept-language-http-header"></a>Kabul Dili HTTP üstbilgisi

[Kabul Dili üstbilgisi](https://www.w3.org/International/questions/qa-accept-lang-locales) çoğu tarayıcıda ayarlanabilir ve başlangıçta kullanıcının dilini belirtmek için tasarlanmıştır. Bu ayar, tarayıcının ne göndermek üzere ayarlandığını veya temel işletim sisteminden devralındığını gösterir. Tarayıcı isteğinden kabul dili HTTP üstbilgisi, kullanıcının tercih ettiği dili algılamanın yanılmaz [bir](https://www.w3.org/International/questions/qa-lang-priorities.en.php)yolu değildir (bkz. Bir üretim uygulaması, bir kullanıcının kültür seçimini özelleştirmesi için bir yol içermelidir.

### <a name="set-the-accept-language-http-header-in-ie"></a>IE'de Kabul Dili HTTP üstbilgisini ayarlama

1. Dişli simgesinden **Internet Seçenekleri'ne**dokunun.

2. **Dillere**dokunun.

    ![İnternet Seçenekleri](localization/_static/lang.png)

3. **Dil Tercihlerini Ayarla'ya**dokunun.

4. **Dil ekle'ye**dokunun.

5. Dili ekleyin.

6. Dile dokunun, ardından **Yukarı Taşı'ya**dokunun.

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a>İçerik-Dil HTTP üstbilgisi

[İçerik-Dil](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) varlık üstbilgisi:

 - Dinleyicilere yönelik dil(ler) tanımlamak için kullanılır.
 - Kullanıcının kendi tercih ettiği dile göre ayırt etmesini sağlar.

Varlık üstleleri hem HTTP isteklerinde hem de yanıtlarında kullanılır.

Üstbilgi `Content-Language` özelliği `ApplyCurrentCultureToResponseHeaders`ayarlayarak eklenebilir.

Üstbilgi `Content-Language` ekleme:

 - RequestLocalizationMiddleware ile `Content-Language` üstbilgi ayarlamak için `CurrentUICulture`izin verir .
 - Yanıt üstbilgisini `Content-Language` açıkça ayarlama gereksinimini ortadan kaldırır.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a>Özel bir sağlayıcı kullanma

Müşterilerinizin dillerini ve kültürlerini veritabanlarınızda depolamasına izin vermek istediğinizi varsayalım. Kullanıcı için bu değerleri aramak için bir sağlayıcı yazabilirsiniz. Aşağıdaki kod, özel bir sağlayıcının nasıl ekleyeceğini gösterir:

::: moniker range="< aspnetcore-3.0"
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
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
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
::: moniker-end

Yerelleştirme sağlayıcıları eklemek veya kaldırmak için kullanın. `RequestLocalizationOptions`

### <a name="set-the-culture-programmatically"></a>Kültürü programlı olarak ayarlayın

[GitHub](https://github.com/aspnet/entropy) bu örnek **Localization.StarterWeb** proje ayarlamak `Culture`için UI içerir. *Views/Shared/_SelectLanguagePartial.cshtml* dosyası, desteklenen kültürler listesinden kültürü seçmenize olanak tanır:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

*Görünümler/Paylaşılan/_SelectLanguagePartial.cshtml* dosyası düzen `footer` dosyasının bölümüne eklenir, böylece tüm görünümler için kullanılabilir olacaktır:

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Yöntem `SetLanguage` kültür çerezayarlar.

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

Bu proje için örnek kod için *_SelectLanguagePartial.cshtml* takamazsınız. [GitHub'daki](https://github.com/aspnet/entropy) **Localization.StarterWeb** projesi, Bağımlılık `RequestLocalizationOptions` [Enjeksiyonu](dependency-injection.md) kabından bir Razor kısmi akışı için koda sahiptir.

## <a name="model-binding-route-data-and-query-strings"></a>Yol verilerini ve sorgu dizelerini model bağlama

Bkz. [Model bağlama rota verileri ve sorgu dizeleri Küreselleşme davranışı.](xref:mvc/models/model-binding#glob)

## <a name="globalization-and-localization-terms"></a>Küreselleşme ve yerelleştirme terimleri

Uygulamanızı yerelleştirme işlemi, modern yazılım geliştirmede yaygın olarak kullanılan alakalı karakter kümelerinin temel bir şekilde anlaşılmasını ve bunlarla ilişkili sorunların anlaşılmasını da gerektirir. Tüm bilgisayarlar metni sayı (kod) olarak depolamak la birlikte, farklı sistemler farklı sayılar kullanarak aynı metni depolar. Yerelleştirme işlemi, uygulama kullanıcı arabiriminin (UI) belirli bir kültür/yerel alan için çevrilmesi anlamına gelir.

[Yerelleştirilebilirlik,](/dotnet/standard/globalization-localization/localizability-review) küreselleştirilmiş bir uygulamanın yerelleştirme için hazır olduğunu doğrulamak için bir ara işlemdir.

Kültür adı için [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) `<languagecode2>-<country/regioncode2>`biçimi `<languagecode2>` , nerede `<country/regioncode2>` dil kodu ve alt kültür kodudur. Örneğin, `es-CL` İspanyolca (Şili), `en-US` İngilizce (AMERIKA Birleşik `en-AU` Devletleri) ve İngilizce (Avustralya) için. [RFC 4646,](https://www.ietf.org/rfc/rfc4646.txt) bir dille ilişkili bir ISO 639 iki harfli küçük harfli kültür kodu ile bir ülke veya bölgeyle ilişkili bir ISO 3166 iki harfli büyük harfli alt kültür kodunun birleşimidir. [Bkz. Dil Kültür Adı](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).

Uluslararasılaşma genellikle "I18N" olarak kısaltılır. Kısaltma ilk ve son harfleri ve aralarındaki harf sayısını alır, bu nedenle 18 ilk "I" ve son "N" arasındaki harf sayısını temsil alır. Aynı şey Küreselleşme (G11N) ve Yerelleştirme (L10N) için de geçerlidir.

Terim:

* Küreselleşme (G11N): Bir uygulamanın farklı dilleri ve bölgeleri desteklemesi süreci.
* Yerelleştirme (L10N): Belirli bir dil ve bölge için bir uygulamayı özelleştirme işlemi.
* Uluslararasılaşma (I18N): Küreselleşmeyi ve yerelleşmeyi açıklar.
* Kültür: Bu bir dil ve isteğe bağlı olarak, bir bölge.
* Tarafsız kültür: Belirli bir dile sahip, ancak bölgeye sahip olmayan bir kültür. (örneğin "en", "es")
* Özel kültür: Belirli bir dil ve bölgeye sahip bir kültür. (örneğin "en-US", "en-GB", "es-CL")
* Üst kültür: Belirli bir kültürü içeren tarafsız kültür. (örneğin, "en" "en-US" ve "en-GB" ana kültürüdür)
* Yerel: Yerel bir kültürle aynıdır.

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb projesi](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) makalede kullanılmıştır.
* [Küreselleştirme ve yerelleştirme .NET uygulamaları](/dotnet/standard/globalization-localization/index)
* [.resx Dosyalarındaki Kaynaklar](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft Çok Dilli Uygulama Araç Seti](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Generics & Yerelleştirme](http://hishambinateya.com/localization-and-generics)
