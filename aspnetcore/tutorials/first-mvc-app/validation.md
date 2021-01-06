---
title: 9. bölüm, ASP.NET Core MVC uygulamasına doğrulama ekleme
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin Bölüm 9 ' da.
ms.author: riande
ms.date: 04/13/2017
no-loc:
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
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 340a66c4a561c6e00bf6f38bcf51abc795aa649c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059097"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a>9. bölüm, ASP.NET Core MVC uygulamasına doğrulama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümdeki konular:

* Doğrulama mantığı `Movie` modele eklenir.
* Bir Kullanıcı bir filmi oluşturduğunda veya düzenleişinizde doğrulama kurallarının uygulanmasını sağlayabilirsiniz.

## <a name="keeping-things-dry"></a>İşleri güncel tutma

MVC ['nin tasarımdan biri ("](https://wikipedia.org/wiki/Don%27t_repeat_yourself) kendini tekrarlama"). ASP.NET Core MVC, işlevselliği veya davranışı yalnızca bir kez belirtmenizi ve bir uygulamada her yerde yansıtıldığını önerir. Bu, yazmanız gereken kod miktarını azaltır ve daha az hata yazmanızı, daha kolay test yapmayı ve bakımını daha kolay hale getirir.

MVC ve Entity Framework Core Code First tarafından sunulan doğrulama desteği, işlem içindeki kuru ilkeye uygun bir örnektir. Doğrulama kurallarını tek bir yerde (model sınıfında) bildirimli olarak belirtebilir ve kurallar uygulamada her yerde zorlanır.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Doğrulama hatası Kullanıcı arabirimi

Uygulamayı çalıştırın ve filmler denetleyicisine gidin.

Yeni bir film eklemek için **Yeni oluştur** bağlantısına dokunun. Formu, bazı geçersiz değerlerle doldurun. JQuery istemci tarafı doğrulaması hatayı algıladıktan hemen sonra bir hata iletisi görüntüler.

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Formun, geçersiz bir değer içeren her bir alanda uygun bir doğrulama hata iletisini nasıl otomatik olarak oluşturduğuna dikkat edin. Hatalar hem istemci tarafında (JavaScript ve jQuery kullanılarak) hem de sunucu tarafında (kullanıcının JavaScript devre dışı bırakılmış olması durumunda) zorlanır.

Önemli bir avantaj, `MoviesController` Bu doğrulama kullanıcı arabirimini etkinleştirmek için sınıfında veya *Create. cshtml* görünümündeki tek bir kod satırını değiştirmeniz gerekmez. Bu öğreticide daha önce oluşturduğunuz denetleyici ve görünümler, model sınıfının özelliklerinde doğrulama özniteliklerini kullanarak belirttiğiniz doğrulama kurallarını otomatik olarak çekti `Movie` . Eylem yöntemini kullanarak test doğrulama `Edit` ve aynı doğrulama uygulandı.

Form verileri, istemci tarafı doğrulama hatası kalmayana kadar sunucuya gönderilmez. Bunu, `HTTP Post` [Fiddler aracını](https://www.telerik.com/fiddler) ya da [F12 geliştirici araçlarını](/microsoft-edge/devtools-guide)kullanarak yöntemine bir kesme noktası koyarak doğrulayabilirsiniz.

## <a name="how-validation-works"></a>Doğrulamanın çalışması

Doğrulama Kullanıcı arabiriminin denetleyici veya görünümlerde kodda herhangi bir güncelleştirme yapmadan nasıl oluşturulduğunu merak edebilirsiniz. Aşağıdaki kod iki `Create` yöntemi gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

İlk (HTTP GET) `Create` eylem yöntemi Ilk oluşturma formunu görüntüler. İkinci ( `[HttpPost]` ) sürüm, form gönderisini işler. İkinci `Create` Yöntem ( `[HttpPost]` sürüm), `ModelState.IsValid` filmin herhangi bir doğrulama hatası olup olmadığını denetlemek için çağırır. Bu yöntemi çağırmak, nesnesine uygulanmış olan tüm doğrulama özniteliklerini değerlendirir. Nesne doğrulama hatalarıyla karşılaşırsanız, `Create` yöntemi formu yeniden görüntüler. Hata yoksa, yöntemi yeni filmi veritabanına kaydeder. Film örneğimizde, istemci tarafında algılanan doğrulama hataları olduğunda form sunucuya nakledilmez; İkinci `Create` Yöntem, istemci tarafı doğrulama hataları olduğunda hiçbir zaman çağrılmaz. Tarayıcınızda JavaScript 'i devre dışı bırakırsanız, istemci doğrulaması devre dışıdır ve `Create` `ModelState.IsValid` herhangi bir doğrulama hatasını tespit etmek IÇIN http post yöntemini test edebilirsiniz.

Yönteminde bir kesme noktası ayarlayabilir `[HttpPost] Create` ve yöntemin hiçbir zaman çağrılmadığını doğrulayabilirsiniz, istemci tarafı doğrulama, doğrulama hataları algılandığında form verilerini göndermez. Tarayıcınızda JavaScript 'i devre dışı bırakır, ardından formu hatalarla gönderirseniz, kesme noktası isabet eder. JavaScript olmadan tam doğrulama almaya devam edersiniz. 

Aşağıdaki görüntüde, Firefox tarayıcısında JavaScript 'In nasıl devre dışı bırakılacağı gösterilmektedir.

![Firefox: Seçenekler ' in Içerik sekmesinde, JavaScript 'ı etkinleştir onay kutusunun işaretini kaldırın.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Aşağıdaki görüntüde, Chrome tarayıcısında JavaScript 'In nasıl devre dışı bırakılacağı gösterilmektedir.

![Google Chrome: Içerik ayarlarının JavaScript bölümünde herhangi bir sitenin JavaScript çalıştırmasına izin verme ' yi seçin.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

JavaScript 'i devre dışı bıraktıktan sonra, geçersiz veri gönderin ve hata ayıklayıcıda adım adım ilerleyin.

![Geçersiz verilerin bir göndermasında hata ayıklarken, ModelState. IsValid üzerinde IntelliSense, değerin false olduğunu gösterir.](~/tutorials/first-mvc-app/validation/_static/ms.png)

*Create. cshtml* görünüm şablonunun bölümü aşağıdaki biçimlendirmede gösterilmiştir:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Yukarıdaki biçimlendirme eylem yöntemleri tarafından ilk formu görüntülemek ve bir hata durumunda onu yeniden görüntülemek için kullanılır.

[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) , [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir. [Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler. Daha fazla bilgi için bkz. [doğrulama](xref:mvc/models/validation) .

Bu yaklaşım ne kadar iyi bir şeydir, denetleyicinin ne de `Create` Görünüm şablonu zorlanmakta olan gerçek doğrulama kuralları veya görüntülenen belirli hata iletileri hakkında herhangi bir şeyi bilmez. Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfında belirtilmiştir. Bu aynı doğrulama kuralları, bir `Edit` görünüme ve modelinizi düzenleyebilecek oluşturabileceğiniz diğer tüm görünümler şablonlarına otomatik olarak uygulanır.

Doğrulama mantığını değiştirmeniz gerektiğinde, modele doğrulama öznitelikleri ekleyerek tam olarak bir yerde bunu yapabilirsiniz (Bu örnekte, `Movie` sınıfı). Kuralların nasıl zorlandığından, uygulamanın farklı bölümlerinin tutarsız olması konusunda endişelenmeniz gerekmez; tüm doğrulama mantığı tek bir yerde tanımlanır ve her yerde kullanılır. Bu, kodun temiz kalmasını sağlar ve bakımını ve gelişmesini kolaylaştırır. Ayrıca, KURULAMA ilkesini tam olarak sunabileceksiniz anlamına gelir.

## <a name="using-datatype-attributes"></a>DataType özniteliklerini kullanma

*Movie.cs* dosyasını açın ve `Movie` sınıfını inceleyin. `System.ComponentModel.DataAnnotations`Ad alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar. `DataType`Yayın tarihine ve fiyat alanlarına bir numaralandırma değeri zaten uyguladık. Aşağıdaki kod, `ReleaseDate` `Price` uygun özniteliğiyle ve özelliklerini gösterir `DataType` .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType`Öznitelikler yalnızca görünüm altyapısının verileri biçimlendirmek için ipuçları sağlar (ve `<a>` URL 'ler ve `<a href="mailto:EmailAddress.com">` e-posta için gibi öğeleri/öznitelikleri sağlar. `RegularExpression`Veri biçimini doğrulamak için özniteliğini kullanabilirsiniz. `DataType`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır, bunlar doğrulama öznitelikleri değildir. Bu durumda, zamanı değil yalnızca tarihi izlemek istiyoruz. `DataType`Sabit listesi, tarih, saat, PhoneNumber, para birimi, Emaadresi ve daha fazlası gibi birçok veri türünü sağlar. `DataType`Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` ve HTML5 'i destekleyen tarayıcılarda için bir tarih seçici sağlaneklenebilir `DataType.Date` . Öznitelikler HTML 5 `DataType` `data-` tarayıcılarının ANLAYABILMESI için HTML 5 (bir veri Dash) öznitelikleri yayar. `DataType`Öznitelikler herhangi bir  doğrulama sağlamaz.

`DataType.Date` görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucu ' a göre varsayılan biçimlere göre görüntülenir `CultureInfo` .

`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode`Ayar, bir metin kutusunda değer görüntülenmek üzere görüntülendiğinde biçimlendirmenin de uygulanacağını belirtir. (Örneğin, para birimi değerleri için, büyük olasılıkla, metin kutusundaki para birimi sembolünü, bazı alanlar için istemiyor olabilirsiniz.)

`DisplayFormat`Özniteliğini kendi başına kullanabilirsiniz, ancak genellikle özniteliği kullanmak iyi bir fikirdir `DataType` . `DataType`Özniteliği, bir ekranda nasıl işlenirim aksine, verilerin semantiğini alır ve DisplayFormat ile elde olmadığınız avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını vb. göstermek için)

* Varsayılan olarak tarayıcı, verileri yerel ayarınızı temel alarak doğru biçimi kullanarak işleyebilir.

* `DataType`Özniteliği, verileri işlemek için doğru alan şablonunu seçmek üzere MVC 'yi etkinleştirebilir ( `DisplayFormat` kendisi tarafından kullanılıyorsa, dize şablonunu kullanıyorsa).

> [!NOTE]
> jQuery doğrulaması, `Range` ve özniteliğiyle çalışmaz `DateTime` . Örneğin, aşağıdaki kod, tarih belirtilen aralıkta olduğunda bile her zaman bir istemci tarafı doğrulama hatası görüntüler:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Özniteliğini ile kullanmak için jQuery Tarih doğrulamasını devre dışı bırakmanız gerekir `Range` `DateTime` . Genellikle, modellerinizde sabit tarihleri derlemek iyi bir uygulamadır, bu nedenle `Range` özniteliğini kullanarak ve `DateTime` önerilmez.

Aşağıdaki kod, öznitelikleri tek bir satırda birleştirmeyi gösterir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

Serinin bir sonraki bölümünde, uygulamayı gözden geçiririz ve otomatik olarak oluşturulan ve yöntemlerinde bazı geliştirmeler yaparsınız `Details` `Delete` .

## <a name="additional-resources"></a>Ek kaynaklar

* [Formlarla Çalışma](xref:mvc/views/working-with-forms)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)
* [Etiket yardımcılarına giriş](xref:mvc/views/tag-helpers/intro)
* [Yazar etiketi yardımcıları](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Önceki](new-field.md) 
>  [Sonraki](details.md)  
