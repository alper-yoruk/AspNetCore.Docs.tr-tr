---
title: ASP.NET Core MVC'de Görünümler
author: ardalis
description: ASP.NET Core MVC'de görünümlerin uygulamanın veri sunumlarını ve kullanıcı etkileşimini nasıl ele ASP.NET öğrenin.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384076"
---
# <a name="views-in-aspnet-core-mvc"></a>ASP.NET Core MVC'de Görünümler

Yazar: [Steve Smith](https://ardalis.com/)

Bu belge, ASP.NET Core MVC uygulamalarında kullanılan görünümleri açıklar. Jilet Sayfaları hakkında bilgi için, [Bkz. Jilet Sayfalarına Giriş](xref:razor-pages/index).

Model-View-Controller (MVC) deseninde, *görünüm* uygulamanın veri sunusunu ve kullanıcı etkileşimini işler. Görünüm, katıştırılmış [Razor biçimlendirmesi](xref:mvc/views/razor)içeren bir HTML şablonudur. Jilet biçimlendirme, istemciye gönderilen bir web sayfası oluşturmak için HTML biçimlendirmesi ile etkileşimedebilen koddur.

Core MVCASP.NET görünümler, Razor biçimlendirmede [C# programlama dilini](/dotnet/csharp/) kullanan *.cshtml* dosyalarıdır. Genellikle, görünüm dosyaları uygulamanın denetleyicilerinin her biri için adlandırılmış [klasörlere](xref:mvc/controllers/actions)gruplandırılmış. Klasörler uygulamanın kökündeki *görünümler* klasöründe depolanır:

![Visual Studio'nun Solution Explorer'ındaki Görünümler klasörü, About.cshtml, Contact.cshtml ve Index.cshtml dosyalarını göstermek için Açık Ana klasörüyle açılır](overview/_static/views_solution_explorer.png)

*Giriş* denetleyicisi, *Görünümler* klasörü içinde bir *Giriş* klasörüyle temsil edilir. *Ana klasör,* *Hakkında,* *İletişim*ve *Dizin* (ana sayfa) web sayfalarının görünümlerini içerir. Bir kullanıcı bu üç web sayfasından birini istediğinde, *Ana Sayfa* denetleyicisindeki denetleyici eylemleri, bir web sayfasını oluşturmak ve kullanıcıya döndürmek için hangi üç görünümden hangisinin kullanıldığını belirler.

Tutarlı web sayfası bölümleri sağlamak ve kod tekrarını azaltmak için [mizanpajları](xref:mvc/views/layout) kullanın. Düzenlergenellikle üstbilgi, gezinti ve menü öğeleri ni ve altbilgiyi içerir. Üstbilgi ve altbilgi genellikle birçok meta veri öğesi ve komut dosyası ve stil varlıklarına bağlantılar için ortak biçimlendirme içerir. Düzenler, görünümlerinizdeki bu ortak plaka biçimlendirmeyi önlemenize yardımcı olur.

[Kısmi görünümler,](xref:mvc/views/partial) yeniden kullanılabilir görünüm bölümlerini yöneterek kod yinelemesini azaltır. Örneğin, kısmi bir görünüm, bir blog web sitesinde çeşitli görünümlerde görünen bir yazar biyografisi için yararlıdır. Yazar biyografisi sıradan görünüm içeriğidir ve web sayfasının içeriğini oluşturmak için kod gerektirmez. Yazar biyografi içeriği yalnızca model bağlama tarafından görünümü kullanılabilir, bu nedenle bu tür içerik için kısmi bir görünüm kullanmak idealdir.

[Görünüm bileşenleri,](xref:mvc/views/view-components) yinelenen kodu azaltmanıza olanak tanıyan kısmi görünümlere benzer, ancak web sayfasını işlemek için kodun sunucuda çalıştırılması nı gerektiren içeriği görüntülemek için uygundur. Görüntü bileşenleri, işlenen içerik bir web sitesi alışveriş sepeti gibi veritabanı etkileşimi gerektirdiğinde yararlıdır. Görünüm bileşenleri, web sayfası çıktısı üretmek için model bağlamayla sınırlı değildir.

## <a name="benefits-of-using-views"></a>Görünümleri kullanmanın yararları

Görünümler, kullanıcı arabirimi biçimlendirmesini uygulamanın diğer bölümlerinden ayırarak bir MVC uygulaması içindeki [endişelerin ayrılmasını](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) belirlemeye yardımcı olur. SoC tasarımını takip etmek, uygulamanızı modüler hale getirir ve bu da çeşitli avantajlar sağlar:

* Uygulamanın bakımı daha kolaydır, çünkü daha iyi düzenlenir. Görünümler genellikle uygulama özelliğine göre gruplandırılır. Bu, bir özellik üzerinde çalışırken ilgili görünümleri bulmayı kolaylaştırır.
* Uygulamanın bölümleri gevşek bir şekilde birleştirilmiş. Uygulamanın görünümlerini iş mantığından ve veri erişim bileşenlerinden ayrı olarak oluşturabilir ve güncelleyebilirsiniz. Uygulamanın diğer bölümlerini güncellemek zorunda kalmadan uygulamanın görünümlerini değiştirebilirsiniz.
* Görünümler ayrı birimler olduğundan uygulamanın kullanıcı arabirimi bölümlerini sınamak daha kolaydır.
* Daha iyi bir organizasyon sayesinde, kullanıcı arabiriminin bölümlerini yanlışlıkla yineleme olasılığınız daha düşüktür.

## <a name="creating-a-view"></a>Görünüm oluşturma

Denetleyiciye özgü görünümler *Görünümler/[ControllerName]* klasöründe oluşturulur. Denetleyiciler arasında paylaşılan görünümler *Görünümler/Paylaşılanlar* klasörüne yerleştirilir. Bir görünüm oluşturmak için yeni bir dosya ekleyin ve *.cshtml* dosya uzantısı ile ilişkili denetleyici eylemiyle aynı adı verin. *Giriş* denetleyicisinde *About* eylemiile karşılık gelen bir görünüm oluşturmak için *Görünümler/Ev* klasöründe *About.cshtml* dosyası oluşturun:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Jilet* leişareti `@` sembolle başlar. Kıvırcık ayraçlar tarafından belirlenen [Razor kod blokları](xref:mvc/views/razor#razor-code-blocks) içine C# kodunu yerleştirerek C# ifadelerini çalıştırın (`{ ... }`). Örneğin, yukarıda gösterildiği "Hakkında" `ViewData["Title"]` atamabakın. Yalnızca `@` sembolle değere başvurarak HTML içindeki değerleri görüntüleyebilirsiniz. Yukarıdaki ve `<h2>` `<h3>` öğelerin içeriğini görün.

Yukarıda gösterilen görüntüleme içeriği, kullanıcıya işlenen tüm web sayfasının yalnızca bir parçasıdır. Sayfanın düzeninin geri kalanı ve görünümün diğer ortak yönleri diğer görünüm dosyalarında belirtilir. Daha fazla bilgi edinmek için [Düzen konusuna](xref:mvc/views/layout)bakın.

## <a name="how-controllers-specify-views"></a>Denetleyiciler görünümleri nasıl belirtir?

Görünümler genellikle [eylemlerden](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)Bir Eylem Sonucu türü olan [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult)olarak döndürülür. Eylem yönteminiz doğrudan bir `ViewResult` yöntem oluşturabilir ve döndürebilir, ancak bu genellikle yapılmaz. Denetleyiciden devralınan çoğu denetleyici [olduğundan,](/dotnet/api/microsoft.aspnetcore.mvc.controller)aşağıdakileri döndürmek için `View` yardımcı yöntemini kullanmanız `ViewResult`yeterlidir:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Bu eylem geri döndüğünde, son bölümde gösterilen *About.cshtml* görünümü aşağıdaki web sayfası olarak işlenir:

![Edge tarayıcısında işlenen sayfa hakkında](overview/_static/about-page.png)

`View` Yardımcı yöntemi birkaç aşırı yükleme vardır. İsteğe bağlı olarak belirtebilirsiniz:

* İade etmek için açık bir görünüm:

  ```csharp
  return View("Orders");
  ```

* Görünüme geçmek için bir [model:](xref:mvc/models/model-binding)

  ```csharp
  return View(Orders);
  ```

* Hem görünüm hem de model:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Keşfi görüntüleme

Bir eylem bir görünüm döndürdüğünde, *görünüm bulma* adı verilen bir işlem gerçekleşir. Bu işlem, görünüm adına göre hangi görünüm dosyasının kullanılacağını belirler. 

`View` Yöntemin varsayılan davranışı`return View();`( ) çağrıldığı eylem yöntemiyle aynı ada sahip bir görünüm döndürmektir. Örneğin, Denetleyicinin *About* `ActionResult` yöntem adı *About.cshtml*adlı bir görünüm dosyası aramak için kullanılır. İlk olarak, çalışma süresi görünüm için *Görünümler/[ControllerName]* klasöründe görünür. Burada eşleşen bir görünüm bulamazsa, görünüm için *Paylaşılan* klasörü arar.

Görünümün `ViewResult` adını `return View();` `View` `return View("<ViewName>");`'' ile birlikte veya açıkça metoduna geçirmeniz önemli değildir. Her iki durumda da, bu sırada eşleşen bir görünüm dosyası için bulma aramalarını görüntüleyin:

   1. *Görünümler/\[ControllerName]/\[ViewName].cshtml*
   1. *Görünümler/Paylaşılan/\[ViewName].cshtml*

Görünüm adı yerine görünüm dosyası yolu sağlanabilir. Uygulama kökünden başlayan mutlak bir yol kullanıyorsanız (isteğe bağlı olarak "/" veya "~/" ile başlıyorsanız), *.cshtml* uzantısı belirtilmelidir:

```csharp
return View("Views/Home/About.cshtml");
```

*.cshtml* uzantısı olmadan farklı dizinlerde görünümleri belirtmek için göreli bir yol da kullanabilirsiniz. `HomeController`İçinde, göreli bir yol ile *Yönet* görünümlerinizin *Dizin* görünümünü döndürebilirsiniz:

```csharp
return View("../Manage/Index");
```

Benzer şekilde, "./" öneki yle geçerli denetleyiciye özgü dizini belirtebilirsiniz:

```csharp
return View("./About");
```

[Kısmi görünümler](xref:mvc/views/partial) ve [görünüm bileşenleri](xref:mvc/views/view-components) benzer (ancak aynı olmayan) bulma mekanizmaları kullanır.

Özel bir [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)kullanarak görünümlerin uygulama içinde nasıl bulunduğuna ilişkin varsayılan kuralı özelleştirebilirsiniz.

Görünüm bulma, görünüm dosyalarını dosya adına göre bulma üzerine dayanır. Temel dosya sistemi büyük/küçük harf duyarlıysa, görünüm adları büyük olasılıkla büyük/küçük harf duyarlıdır. İşletim sistemleri arasında uyumluluk için, denetleyici ve eylem adları ile ilişkili görünüm klasörleri ve dosya adları arasındaki büyük/küçük harf le eşleştirin. Büyük/küçük harf duyarlı bir dosya sistemiyle çalışırken bir görünüm dosyasının bulunamayacağı bir hatayla karşılaşırsanız, istenen görünüm dosyası ile gerçek görünüm dosyası adı arasındaki kasanın eşleştiğini doğrulayın.

Denetimörler, eylemler ve görünümler arasındaki ilişkileri yansıtacak şekilde görünümleriniz için dosya yapısını düzenlemenin en iyi uygulamasını izleyin.

## <a name="passing-data-to-views"></a>Verileri görünümlere aktarma

Verileri çeşitli yaklaşımlar kullanarak görünümlere aktarın:

* Güçlü bir şekilde yazılan veriler: viewmodel
* Zayıf yazılan veriler
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Güçlü bir şekilde yazılan veriler (görünüm modeli)

En sağlam yaklaşım, görünümde bir [model](xref:mvc/models/model-binding) türü belirtmektir. Bu model genellikle bir *görünüm modeli*olarak adlandırılır. Görünüm modeli türünün bir örneğini eylemden görünüme geçirirsiniz.

Verileri bir görünüme aktarmak için bir görünüm modeli kullanmak, görünümün *güçlü* tür denetiminden yararlanmasını sağlar. *Güçlü yazma* (veya *güçlü bir şekilde yazılmış)* her değişken ve sabitin `string` `int`açıkça `DateTime`tanımlanmış bir türü olduğu anlamına gelir (örneğin, , , veya). Görünümde kullanılan türlerin geçerliliği derleme zamanında denetlenir.

[Visual Studio](https://visualstudio.microsoft.com) ve [Visual Studio Code](https://code.visualstudio.com/) listesi güçlü [IntelliSense](/visualstudio/ide/using-intellisense)adlı bir özellik kullanarak sınıf üyeleri yazdı. Bir görünüm modelinin özelliklerini görmek istediğinizde, bir dönem (`.`. Bu, daha az hatayla kodu daha hızlı yazmanıza yardımcı olur.

Yönergeyi kullanarak `@model` bir model belirtin. Aşağıdakilerle `@Model`modeli kullanın:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Modeli görünüme sağlamak için denetleyici modeli bir parametre olarak geçirir:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Görünüme sağlayabileceğiniz model türleri üzerinde herhangi bir kısıtlama yoktur. Çok az davranış (yöntem) tanımlı Düz Eski CLR Nesnesi (POCO) görünüm modellerini kullanmanızı öneririz. Genellikle, görünüm modeli sınıfları *Modeller* klasöründe veya uygulamanın kökünde ayrı bir *Görünüm Modelleri* klasöründe depolanır. Yukarıdaki örnekte kullanılan *Adres* görünüm modeli *Address.cs*adlı bir dosyada depolanan bir POCO görünüm modelidir:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Hiçbir şey hem görünüm model türleri niz hem de iş modeli türleriniz için aynı sınıfları kullanmanızı engellemez. Ancak, ayrı modeller kullanmak, görünümlerinizin iş mantığından ve uygulamanızın veri erişim bölümlerinden bağımsız olarak değişmesini sağlar. Modellerin ve görünüm modellerinin ayrılması, modeller de kullanıcı tarafından uygulamaya gönderilen veriler için [model bağlama](xref:mvc/models/model-binding) ve [doğrulama](xref:mvc/models/validation) kullandığında güvenlik avantajları sunar.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Zayıf yazılan veriler (ViewData, ViewData özniteliği ve ViewBag)

`ViewBag`*Razor Pages'da kullanılamaz.*

Güçlü bir şekilde yazılan görünümlere ek olarak, görünümler *zayıf yazılan* *(gevşek olarak yazılan)* veri koleksiyonuna erişebilir. Güçlü türlerin aksine, *zayıf türler* (veya gevşek *türler),* kullandığınız veri türünü açıkça bildirmediğiniz anlamına gelir. Denetleyicilere ve görünümlere küçük miktarlarda veri aktarmak için zayıf yazılan verilerin toplanmasını kullanabilirsiniz.

| Arasında veri geçirme ...                        | Örnek                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Denetleyici ve görünüm                             | Bir açılır listeyi verilerle doldurma.                                          |
| Görünüm ve [düzen görünümü](xref:mvc/views/layout)   | ** \<Bir** görünüm dosyasından düzen görünümünde başlık>öğesi içeriğini ayarlama.  |
| [Kısmi görünüm](xref:mvc/views/partial) ve görünüm | Kullanıcının istediği web sayfasına göre verileri görüntüleyen bir widget.      |

Bu koleksiyon, denetleyiciler `ViewData` ve `ViewBag` görünümler üzerindeki özellikler veya bu koleksiyon aracılığıyla başvurulabilir. Özellik, `ViewData` zayıf yazılan nesnelerin sözlüğüdür. Özellik, `ViewBag` altta yatan `ViewData` `ViewData` koleksiyon için dinamik özellikler sağlayan bir sarmalayıcıdır. Not: Anahtar aramaları her ikisi için `ViewData` `ViewBag`de büyük/küçük harf duyarsızdır ve .

`ViewData`ve `ViewBag` çalışma zamanında dinamik olarak çözülür. Derleme zamanı türü denetimi sunmadıkları için, her ikisi de genellikle bir görünüm modeli kullanmaktan daha fazla hataya eğilimlidir. Bu nedenle, bazı geliştiriciler minimal veya `ViewData` hiç `ViewBag`kullanmayı tercih ve.

<a name="VD"></a>

**Viewdata**

`ViewData`anahtarlar aracılığıyla `string` erişilen bir [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) nesnesidir. Dize verileri döküme gerek kalmadan doğrudan depolanabilir ve doğrudan `ViewData` kullanılabilir, ancak bunları ayıklarken diğer nesne değerlerini belirli türlere dökmeniz gerekir. Denetimlerden `ViewData` görünümlere ve [kısmi görünümler](xref:mvc/views/partial) ve [düzenler](xref:mvc/views/layout)de dahil olmak üzere görünümler içinde veri aktarmak için kullanabilirsiniz.

Bir eylemde kullanarak `ViewData` bir karşılama ve adres için değerleri ayarlayan bir örnek aşağıda verilmiştir:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Görünümdeki verilerle çalışın:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**ViewData özniteliği**

[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) kullanan bir diğer yaklaşım [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)olduğunu. Öznitelik ile `[ViewData]` işaretlenmiş denetleyiciler veya Razor Page modellerindeki özelliklerin değerleri sözlükten depolanır ve yüklenir.

Aşağıdaki örnekte, Ev denetleyicisi `Title` ile `[ViewData]`işaretlenmiş bir özellik içerir. Yöntem `About` Hakkında görünümü için başlığı ayarlar:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

Düzende, başlık ViewData sözlüğünden okunur:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**Görünüm Çantası**

`ViewBag`*Razor Pages'da kullanılamaz.*

`ViewBag`'de depolanan nesnelere dinamik erişim sağlayan bir `ViewData` [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) nesnesidir. `ViewBag`döküm gerektirmediğinden, çalışmak için daha uygun olabilir. Aşağıdaki örnek, yukarıdaki `ViewBag` gibi `ViewData` aynı sonuçla nasıl kullanılacağını gösterir:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**ViewData ve ViewBag'i aynı anda kullanma**

`ViewBag`*Razor Pages'da kullanılamaz.*

Bu `ViewData` `ViewBag` yana ve aynı `ViewData` temel toplama bakın, hem `ViewData` ve ve karıştırmak ve `ViewBag` aralarında maç zaman okuma ve yazma değerleri.

*About.cshtml* görünümünün `ViewData` üst kısmındaki başlığı ve açıklamayı kullanarak `ViewBag` ayarlayın:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Özellikleri okuyun ama kullanımı `ViewData` ters `ViewBag`ve . *_Layout.cshtml* dosyasında, başlığı kullanarak `ViewData` edinin ve `ViewBag`açıklamayı kullanarak edinin:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Dizeleri için `ViewData`bir döküm gerektirmez unutmayın. Döküm olmadan `@ViewData["Title"]` kullanabilirsiniz.

Her `ViewData` ikisini `ViewBag` de ve aynı anda kullanmak, karıştırma ve eşleştirme okuma ve özellikleri yazma gibi çalışır. Aşağıdaki biçimlendirme işlenir:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**ViewData ve ViewBag arasındaki farkların özeti**

 `ViewBag`Razor Pages'da kullanılamaz.

* `ViewData`
  * [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)türetilmiştir , bu yüzden yararlı olabilir sözlük `ContainsKey`özellikleri `Add` `Remove`vardır, `Clear`gibi , , , ve .
  * Sözlükteki anahtarlar dizeleri, bu nedenle beyazboşluk izin verilir. Örnek: `ViewData["Some Key With Whitespace"]`
  * A dışındaki herhangi `string` bir tür kullanmak `ViewData`için görünümde döküm olmalıdır.
* `ViewBag`
  * [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)türetilmiştir , böylece nokta gösterimi kullanarak dinamik özellikleri`@ViewBag.SomeKey = <value or object>`oluşturulmasını sağlar ( ), ve hiçbir döküm gereklidir. Sözdizimi `ViewBag` denetleyicilere ve görünümlere eklemeyi daha hızlı yapar.
  * Null değerlerini denetlemek daha kolaydır. Örnek: `@ViewBag.Person?.Name`

**ViewData veya ViewBag ne zaman kullanılır?**

Her `ViewData` `ViewBag` ikisi de ve denetleyicileri ve görünümleri arasında az miktarda veri aktarmak için eşit derecede geçerli yaklaşımlardır. Hangisinin kullanılacağı tercihi tercihe bağlıdır. Ancak, tutarlı `ViewData` bir `ViewBag` şekilde kullanılan bir yaklaşımla kodun okunması ve bakımı daha kolaydır. Her iki yaklaşım da çalışma zamanında dinamik olarak çözülür ve böylece çalışma zamanı hatalarına neden olur. Bazı geliştirme ekipleri bunlardan kaçınır.

### <a name="dynamic-views"></a>Dinamik görünümler

Bir model türünü kullanarak `@model` bildirmeyan ancak bir model örneği onlara geçirilen `return View(Address);`görünümler (örneğin,) örneğin özelliklerine dinamik olarak başvurulabilir:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Bu özellik esneklik sunar, ancak derleme koruması veya IntelliSense sunmaz. Özellik yoksa, web sayfası oluşturma çalışma zamanında başarısız olur.

## <a name="more-view-features"></a>Daha fazla görünüm özellikleri

[Tag Yardımcıları,](xref:mvc/views/tag-helpers/intro) varolan HTML etiketlerine sunucu tarafı davranışı eklemeyi kolaylaştırır. Tag Helpers'ı kullanmak, görünümleriniz içinde özel kod veya yardımcı yazma gereksinimini önler. Etiket yardımcıları HTML öğelerine öznitelikleri olarak uygulanır ve bunları işleyen düzenleyiciler tarafından yoksayılır. Bu, çeşitli araçlarda görünüm biçimlendirmesini oluşturmanıza ve oluşturmanıza olanak tanır.

Birçok yerleşik HTML Yardımcısı ile özel HTML biçimlendirmesi oluşturulabilir. Daha karmaşık kullanıcı arabirimi mantığı [Görünüm Bileşenleri](xref:mvc/views/view-components)tarafından işlenebilir. Görünüm bileşenleri, denetleyicilerin ve görünümlerin sunduğu aynı SoC'yi sağlar. Ortak kullanıcı arabirimi öğeleri tarafından kullanılan verilerle ilgili eylem ve görünüm gereksinimini ortadan kaldırabilirler.

ASP.NET Core'un diğer pek çok yönü gibi, görünümler de [bağımlılık enjeksiyonuna](xref:fundamentals/dependency-injection)destek vererek, hizmetlerin [görünümlere enjekte edilmesine](xref:mvc/views/dependency-injection)olanak sağlar.
