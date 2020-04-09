---
title: ASP.NET Core MVC uygulamasına doğrulama ekleme
author: rick-anderson
description: ASP.NET Core uygulamasına doğrulama ekleme.
ms.author: riande
ms.date: 04/13/2017
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: ecf3d011b38347eb32020df00e44d93ca789443a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242542"
---
# <a name="add-validation-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına doğrulama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde:

* Doğrulama mantığı modele `Movie` eklenir.
* Doğrulama kurallarının, bir kullanıcı bir film oluşturduğunda veya her zaman herhangi bir şekilde uygulandığından emin olabilirsiniz.

## <a name="keeping-things-dry"></a>Şeyler KURU tutmak

MVC'nin tasarım ilkelerinden biri [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) 'dir ("Kendinizi Tekrar etmeyin"). ASP.NET Core MVC işlevselliği veya davranışı yalnızca bir kez belirtmenizi ve ardından bir uygulamaya her yere yansıtılmanızı öneririz. Bu, yazmanız gereken kod miktarını azaltır ve yazdığınız kodu daha az hataya yatkın, sınaması ve bakımı daha kolay hale getirir.

MVC ve Entity Framework Core Code First tarafından sağlanan doğrulama desteği, DRY ilkesinin eyleme uygun olmasının iyi bir örneğidir. Doğrulama kurallarını tek bir yerde (model sınıfında) bildirimsel olarak belirtebilirsiniz ve kurallar uygulamanın her yerinde uygulanır.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Doğrulama Hatası UI

Uygulamayı çalıştırın ve Filmler denetleyicisine gidin.

Yeni bir film eklemek için **Yeni Oluştur** bağlantısına dokunun. Formu bazı geçersiz değerlerle doldurun. JQuery istemci tarafı doğrulama sıyrık hatasını algılar algılamaz, bir hata iletisi görüntüler.

![Birden çok jQuery istemci yan doğrulama hataları ile film görünümü formu](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Formun geçersiz bir değer içeren her alanda uygun bir doğrulama hatası iletisini otomatik olarak nasıl oluşturduğuna dikkat edin. Hatalar hem istemci tarafı (JavaScript ve jQuery kullanarak) hem de sunucu tarafı (kullanıcının JavaScript devre dışı olması durumunda) uygulanır.

Önemli bir yararı, bu doğrulama UI etkinleştirmek için `MoviesController` sınıfta veya *Create.cshtml* görünümünde kod tek bir satırı değiştirmeniz gerekmez. Bu öğreticide daha önce oluşturduğunuz denetleyici ve görünümler, `Movie` model sınıfının özellikleri üzerinde doğrulama özniteliklerini kullanarak belirttiğiniz doğrulama kurallarını otomatik olarak aldı. Eylem yöntemini `Edit` kullanarak test doğrulama ve aynı doğrulama uygulanır.

İstemci tarafı doğrulama hatası olmadan form verileri sunucuya gönderilmez. Bunu, `HTTP Post` [fiddler aracını](https://www.telerik.com/fiddler) veya [F12 Geliştirici araçlarını](/microsoft-edge/devtools-guide)kullanarak yönteme bir kesme noktası koyarak doğrulayabilirsiniz.

## <a name="how-validation-works"></a>Doğrulama nasıl çalışır?

Denetleyicideki veya görünümlerde kodda herhangi bir güncelleştirme olmadan doğrulama ui'sinin nasıl oluşturulduğunu merak edebilirsiniz. Aşağıdaki kod iki `Create` yöntemi gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

İlk (HTTP GET) `Create` eylem yöntemi ilk Oluşturma formunu görüntüler. İkinci (`[HttpPost]`) sürüm form gönderisini işler. İkinci `Create` yöntem (sürüm) `[HttpPost]` `ModelState.IsValid` filmin doğrulama hatası olup olmadığını denetlemek için çağırır. Bu yöntemi çağırmak, nesneye uygulanan tüm doğrulama özniteliklerini değerlendirir. Nesnenin doğrulama hataları varsa, `Create` yöntem formu yeniden görüntüler. Hata yoksa, yöntem yeni filmi veritabanına kaydeder. Film örneğimizde, istemci tarafında algılanan doğrulama hataları olduğunda form sunucuya gönderilmez; istemci `Create` tarafı doğrulama hataları olduğunda ikinci yöntem asla çağrılmaz. Tarayıcınızda JavaScript'i devre dışı bıraktıysanız, istemci doğrulama devre dışı `Create` `ModelState.IsValid` bırakılır ve http post yöntemini herhangi bir doğrulama hatası algılayarak test edebilirsiniz.

`[HttpPost] Create` Yöntemde bir kesme noktası ayarlayabilir ve yöntemin hiçbir zaman çağrılamayacağını doğrulayabilirsiniz, doğrulama hataları algılandığında istemci tarafı doğrulama form verilerini göndermez. Tarayıcınızda JavaScript'i devre dışı bıraktıysanız, formu hatalarla birlikte gönderin, kesme noktası vurulur. Hala JavaScript olmadan tam doğrulama olsun. 

Aşağıdaki resim, Firefox tarayıcısında JavaScript'in nasıl devre dışı kısıdığını gösterir.

![Firefox: Seçeneklerin İçerik sekmesinde, Javascript'i Etkinleştir onay kutusunun üzerindeki işaretin üzerindekini kaldırın.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Aşağıdaki resim, Chrome tarayıcısında JavaScript'in nasıl devre dışı kalındığını gösterir.

![Google Chrome: İçerik ayarlarının Javascript bölümünde, herhangi bir sitenin JavaScript çalışmasına izin verme seçeneğini belirleyin.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

JavaScript'i devre dışı bıraktıktan sonra geçersiz verileri deftere nakledin ve hata ayıklayıcıya adım atın.

![Geçersiz veri gönderisinde hata ayıklarken, ModelState.IsValid'teki Intellisense, değerin yanlış olduğunu gösterir.](~/tutorials/first-mvc-app/validation/_static/ms.png)

*Create.cshtml* görünüm şablonunun bölümü aşağıdaki biçimlendirmede gösterilir:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Önceki biçimlendirme, ilk formu görüntülemek ve bir hata durumunda yeniden görüntülemek için eylem yöntemleri tarafından kullanılır.

[Giriş Etiketi Yardımcısı,](xref:mvc/views/working-with-forms) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulaması için gerekli HTML özniteliklerini üretir. [Doğrulama Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler. Daha fazla bilgi için [Doğrulama'ya](xref:mvc/models/validation) bakın.

Bu yaklaşımın en güzel yanı, ne denetleyicinin ne de görünüm şablonunun, `Create` uygulanan gerçek doğrulama kuralları veya görüntülenen belirli hata iletileri hakkında hiçbir şey bulaşamasıdır. Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfta belirtilir. Bu aynı doğrulama kuralları, görünüme `Edit` ve modelinizi düzenlemeyi oluşturabileceğiniz diğer görünüm şablonlarına otomatik olarak uygulanır.

Doğrulama mantığını değiştirmeniz gerektiğinde, bunu modele (bu örnekte sınıf) `Movie` doğrulama öznitelikleri ekleyerek tam olarak tek bir yerde yapabilirsiniz. Uygulamanın farklı bölümlerinin kuralların nasıl uygulandığıyla tutarsız olması konusunda endişelenmenize gerek kalmaz - tüm doğrulama mantığı tek bir yerde tanımlanır ve her yerde kullanılır. Bu, kodu çok temiz tutar ve bakımı ve geliştirmeyi kolaylaştırır. Bu da DRY prensibini tam olarak yerine getireceğiniz anlamına gelir.

## <a name="using-datatype-attributes"></a>DataType Özniteliklerini Kullanma

*Movie.cs* dosyasını açın `Movie` ve sınıfı inceleyin. Ad `System.ComponentModel.DataAnnotations` alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar. Çıkış tarihine ve `DataType` fiyat alanlarına numaralandırma değeri zaten uyguladık. Aşağıdaki kod uygun `ReleaseDate` `DataType` `Price` öznitelik ile özellikleri ve özellikleri gösterir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Öznitelikler yalnızca `DataType` görünüm altyapısının verileri biçimlendirmesi (ve URL'ler ve `<a>` `<a href="mailto:EmailAddress.com">` e-postalar gibi öğeleri/öznitelikleri) sağlaması için ipuçları sağlar. Verilerin biçimini `RegularExpression` doğrulamak için özniteliği kullanabilirsiniz. Öznitelik `DataType` veritabanı içsel türünden daha özel bir veri türü belirtmek için kullanılır, bunlar doğrulama öznitelikleri değildir. Bu durumda biz sadece tarih değil, zaman takip etmek istiyorum. Numaralandırma, `DataType` Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi ve daha fazlası gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır. Örneğin, HTML5'i destekleyen tarayıcılarda bir `mailto:` bağlantı oluşturulabilir `DataType.Date` `DataType.EmailAddress`ve tarih seçici kullanılabilir. HTML `DataType` 5 `data-` (telaffuz edilen veri tiresi) öznitelikleri, HTML 5 tarayıcılarının anlayabileceği öznitelikleri yayır. Öznitelikler `DataType` herhangi bir doğrulama **sağlamaz.**

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucunun `CultureInfo`.

Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Ayar, `ApplyFormatInEditMode` değer düzenleme için bir metin kutusunda görüntülendiğinde biçimlendirmenin de uygulanması gerektiğini belirtir. (Bazı alanlar için bunu istemeyebilirsiniz — örneğin, para birimi değerleri için, büyük olasılıkla düzenleme için metin kutusundaki para birimi simgesini istemezsiniz.)

Özniteliği tek `DisplayFormat` başına kullanabilirsiniz, ancak özniteliği kullanmak `DataType` genellikle iyi bir fikirdir. Bu `DataType` özellik, verilerin bir ekranda nasıl işlenir, aksine anlamsallarını iletir ve DisplayFormat ile elde edilemediğiniz aşağıdaki avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları, vb. göstermek için)

* Varsayılan olarak, tarayıcı verileri bulunduğunuz yerin temeline göre doğru biçimi kullanarak işler.

* Öznitelik, `DataType` MVC'nin verileri işlemek için doğru alan `DisplayFormat` şablonunu seçmesini sağlayabilir (eğer kendisi tarafından kullanılıyorsa dize şablonu kullanılır).

> [!NOTE]
> jQuery doğrulama özniteliği ile `Range` çalışmıyor ve `DateTime`. Örneğin, tarih belirtilen aralıkta olsa bile, aşağıdaki kod her zaman bir istemci yan doğrulama hatası görüntüler:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Bu özniteliği kullanmak için jQuery tarih doğrulamasını `Range` devre `DateTime`dışı bırakmanız gerekir. Genellikle modellerinizde zor tarihleri derlemek iyi bir uygulama değildir, `Range` bu nedenle `DateTime` özniteliğini kullanarak ve önerilmez.

Aşağıdaki kod, öznitelikleri tek bir satırda birleştirir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

Serinin bir sonraki bölümünde, uygulamayı gözden geçiriyor ve otomatik olarak `Details` oluşturulan `Delete` ve yöntemlerde bazı iyileştirmeler yapıyoruz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Formlarla Çalışma](xref:mvc/views/working-with-forms)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)
* [Tag Helpers Giriş](xref:mvc/views/tag-helpers/intro)
* [Yazar Etiketi Yardımcıları](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Önceki](new-field.md)
> [Sonraki](details.md)  
