---
title: ASP.NET Core Razor Sayfasına doğrulama ekleme
author: rick-anderson
description: ASP.NET Core'daki Bir Razor Page'e doğrulamanın nasıl ekleyeceğinizi keşfedin.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666021"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>ASP.NET Core Razor Sayfasına doğrulama ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde modele `Movie` doğrulama mantığı eklenir. Doğrulama kuralları, bir kullanıcı bir film oluşturduğunda veya her zaman geçerlilenir.

## <a name="validation"></a>Doğrulama

Yazılım geliştirmenin önemli bir ilkesi [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**our self") olarak adlandırılır. Razor Pages, işlevselliğin bir kez belirtildiği ve uygulamanın tüm ekine yansıtıldığı şekilde geliştirmeyi teşvik eder. DRY yardımcı olabilir:

* Bir uygulamadaki kod miktarını azaltın.
* Kodu daha az hataya yatkın hale getirin ve sınanması ve bakımı daha kolay.

Razor Pages ve Entity Framework tarafından sağlanan doğrulama desteği, DRY ilkesinin iyi bir örneğidir. Doğrulama kuralları bildirimsel olarak tek bir yerde (model sınıfında) belirtilir ve kurallar uygulamanın her yerinde uygulanır.

## <a name="add-validation-rules-to-the-movie-model"></a>Film modeline doğrulama kuralları ekleme

DataAnnotations ad alanı, bir sınıfa veya özelliğe bildirimsel olarak uygulanan yerleşik doğrulama öznitelikleri kümesi sağlar. DataAnnotations da biçimlendirme `DataType` ile yardımcı gibi biçimlendirme öznitelikleri içerir ve herhangi bir doğrulama sağlamaz.

Yerleşik `Movie` `Required`, , `StringLength`ve `RegularExpression` `Range` doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulandıkları model özellikleriüzerinde zorlamak istediğiniz davranışı belirtir:

* Ve `Required` `MinimumLength` öznitelikleri bir özelliğin bir değeri olması gerektiğini gösterir; ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için beyaz alan girmesini engellemez.
* Öznitelik, `RegularExpression` hangi karakterlerin giriş olabileceğini sınırlamak için kullanılır. Önceki kodda, "Tür":

  * Sadece harfleri kullanmalısınız.
  * İlk harfin büyük harf olması gerekir. Beyaz boşluk, sayılar ve özel karakterlere izin verilmez.

* `RegularExpression` "Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki alanlarda özel karakterlere ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "Tür" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* Öznitelik, `StringLength` dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en az uzunluğunu ayarlamanızı sağlar.
* Değer türleri (, `int` `float`, `DateTime`, , ) doğal `[Required]` `decimal`olarak gereklidir ve öznitelik gerekmez.

doğrulama kurallarının ASP.NET Core tarafından otomatik olarak uygulanması, uygulamanızın daha sağlam olmasına yardımcı olur. Ayrıca, bir şeyi doğrulamayı ve yanlışlıkla kötü verilerin veritabanına girmesine izin vermeyi unutmamanızı sağlar.

### <a name="validation-error-ui-in-razor-pages"></a>Razor Sayfalarda Doğrulama Hatası UI

Uygulamayı çalıştırın ve Sayfalar/Filmler'e gidin.

Yeni **Oluştur** bağlantısını seçin. Formu bazı geçersiz değerlerle tamamlayın. jQuery istemci tarafı doğrulama hatası algılar, bir hata iletisi görüntüler.

![Birden çok jQuery istemci tarafı doğrulama hatası olan film görüntüleme formu](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Formun geçersiz bir değer içeren her alanda otomatik olarak bir doğrulama hatası iletisi nasıl oluşturduğuna dikkat edin. Hatalar hem istemci tarafı (JavaScript ve jQuery kullanarak) hem de sunucu tarafı (bir kullanıcı JavaScript devre dışı olduğunda) uygulanır.

Önemli bir yararı, Oluştur veya Edit sayfalarında **hiçbir** kod değişikliği gerekli olmasıdır. Modele DataAnnotations uygulandıktan sonra doğrulama UI'si etkinleştirildi. Bu öğreticide oluşturulan Razor Pages, doğrulama kurallarını otomatik olarak aldı `Movie` (model sınıfının özellikleri üzerinde doğrulama öznitelikleri ni kullanarak). Edit sayfasını kullanarak test doğrulama, aynı doğrulama uygulanır.

İstemci tarafı doğrulama hatası olmadan form verileri sunucuya nakledilemiyor. Form verilerinin aşağıdaki yaklaşımlardan biri veya birkaçı tarafından yayınlanmadığını doğrulayın:

* `OnPostAsync` Yönteme bir kesme noktası koyun. Formu gönderin **(Oluştur** veya **Kaydet'i**seçin ). Kırılma noktası asla vurulmaz.
* [Fiddler aracını](https://www.telerik.com/fiddler)kullanın.
* Ağ trafiğini izlemek için tarayıcı geliştirici araçlarını kullanın.

### <a name="server-side-validation"></a>Sunucu tarafı doğrulama

JavaScript tarayıcıda devre dışı bırakıldığında, formu hatalarla göndermek sunucuya gönderir.

İsteğe bağlı, test sunucu tarafı doğrulama:

* Tarayıcıda JavaScript'i devre dışı bıraktı. Tarayıcının geliştirici araçlarını kullanarak JavaScript'i devre dışı kullanabilirsiniz. Tarayıcıda JavaScript'i devre dışı atamıyorsanız, başka bir tarayıcı deneyin.
* Oluştur veya Yap `OnPostAsync` sayfasının yönteminde bir kesme noktası ayarlayın.
* Geçersiz verileriçeren bir form gönderin.
* Model durumunun geçersiz olduğunu doğrulayın:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Alternatif olarak, [istemci tarafı doğrulamayı sunucuda devre dışı atabilirsiniz.](xref:mvc/models/validation#disable-client-side-validation)

Aşağıdaki kod, öğreticide daha önce scaffolded *Create.cshtml* sayfasının bir bölümünü gösterir. İlk formu görüntülemek ve bir hata durumunda formu yeniden görüntülemek için Oluştur ve Düzenleme sayfaları tarafından kullanılır.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Giriş Etiketi Yardımcısı,](xref:mvc/views/working-with-forms) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulaması için gerekli HTML özniteliklerini üretir. [Doğrulama Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler. Daha fazla bilgi için [Doğrulama'ya](xref:mvc/models/validation) bakın.

Oluştur ve Edet sayfalarında doğrulama kuralları yoktur. Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfta belirtilir. Bu doğrulama kuralları, `Movie` modeli düzenlemeyapan Razor Pages'e otomatik olarak uygulanır.

Doğrulama mantığının değişmesi gerektiğinde, yalnızca modelde yapılır. Doğrulama uygulama boyunca tutarlı bir şekilde uygulanır (doğrulama mantığı tek bir yerde tanımlanır). Tek bir yerde doğrulama, kodun temiz tutulmasına yardımcı olur ve korumayı ve güncelleştirmeyi kolaylaştırır.

## <a name="using-datatype-attributes"></a>DataType Özniteliklerini Kullanma

`Movie` Sınıfı inceleyin. Ad `System.ComponentModel.DataAnnotations` alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar. `DataType` özniteliği `ReleaseDate` ve `Price` özelliklerine uygulanır.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Öznitelikler yalnızca `DataType` görünüm altyapısının verileri biçimlendirmesi için ipuçları `<a>` sağlar (ve `<a href="mailto:EmailAddress.com">` URL'ler ve e-postalar gibi öznitelikleri sağlar). Verilerin `RegularExpression` biçimini doğrulamak için özniteliği kullanın. Öznitelik, `DataType` veritabanı içsel türünden daha spesifik bir veri türünü belirtmek için kullanılır. `DataType`öznitelikleri doğrulama öznitelikleri değildir. Örnek uygulamada, yalnızca tarih, zaman olmadan görüntülenir.

Numaralandırma, `DataType` Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi ve daha fazlası gibi birçok veri türü sağlar. Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır. Örneğin, bir `mailto:` bağlantı için `DataType.EmailAddress`oluşturulabilir. `DataType.Date` HTML5'i destekleyen tarayıcılarda bir tarih seçici sağlanabilir. Öznitelikler, `DataType` HTML 5 `data-` tarayıcılarının tükettiği HTML 5 (belirgin veri tiresi) özniteliklerini yayır. Öznitelikler `DataType` herhangi bir doğrulama **sağlamaz.**

`DataType.Date`görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucunun `CultureInfo`.

Entity `[Column(TypeName = "decimal(18, 2)")]` Framework Core'un veritabanındaki para birimiyle `Price` doğru şekilde eşlenebilmeleri için veri ek açıklamagereklidir. Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)

Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Ayar, `ApplyFormatInEditMode` düzenleme için değer görüntülendiğinde biçimlendirmenin uygulanması gerektiğini belirtir. Bazı alanlar için bu davranışı istemeyebilirsiniz. Örneğin, para birimi değerlerinde, büyük olasılıkla eski iş bir arada geçici iş yerinde para birimi simgesini istemezsiniz.

Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir, ancak özniteliği kullanmak `DataType` genellikle iyi bir fikirdir. Bu `DataType` özellik, verilerin bir ekranda nasıl işlenir, aksine anlamsallarını iletir ve DisplayFormat ile elde edilemediğiniz aşağıdaki avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları, vb. göstermek için)
* Varsayılan olarak, tarayıcı verileri bulunduğunuz yerin temeline göre doğru biçimi kullanarak işler.
* Öznitelik, `DataType` ASP.NET Core çerçevesinin verileri işlemek için doğru alan şablonu seçmesini sağlayabilir. Kendisi `DisplayFormat` tarafından kullanılan if dize şablonu kullanır.

Not: jQuery doğrulama öznitelik ile `Range` çalışmıyor `DateTime`ve . Örneğin, tarih belirtilen aralıkta olsa bile, aşağıdaki kod her zaman istemci tarafı doğrulama hatası görüntüler:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Genellikle modellerinizde zor tarihleri derlemek iyi bir uygulama değildir, `Range` bu nedenle `DateTime` özniteliğini kullanarak ve önerilmez.

Aşağıdaki kod, öznitelikleri tek bir satırda birleştirir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor Pages ve EF Core ile başlayın,](xref:data/ef-rp/intro) Razor Pages ile gelişmiş EF Core operasyonlarını gösterir.

### <a name="apply-migrations"></a>Geçişleri uygulama

Sınıfa uygulanan DataAnnotations şemayı değiştirir. Örneğin, `Title` alana uygulanan DataAnnotations:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Karakterleri 60 ile sınırlar.
* Değer vermiyor. `null`

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tablo `Movie` şu anda aşağıdaki şema vardır:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

Önceki şema değişiklikleri EF'nin bir özel durum atmasını neden etmez. Ancak, şema modeli ile tutarlı olması için bir geçiş oluşturun.

**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.
PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database`sınıfın `Up` yöntemlerini `New_DataAnnotations` çalıştırın. Yöntemi `Up` inceleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Güncelleştirilmiş `Movie` tabloda aşağıdaki şema vardır:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

SQLite için geçiş ler gerekli değildir.

---

### <a name="publish-to-azure"></a>Azure’da Yayımlama

Azure'a dağıtım hakkında daha fazla bilgi için [Bkz. Tutorial: SQL Veritabanı ile Azure'da ASP.NET Core uygulaması oluşturun.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

Razor Pages bu giriş tamamladığınız için teşekkür ederiz. [Razor Pages ve EF Core ile başlayın](xref:data/ef-rp/intro) bu öğretici için mükemmel bir takip.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Bu öğreticinin YouTube sürümü](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Önceki: Yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)
