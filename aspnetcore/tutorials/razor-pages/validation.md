---
title: 8. bölüm, doğrulama ekleme
author: rick-anderson
description: Sayfalardaki eğitim serisinin 5. bölümü Razor .
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570230"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Sayfalardaki eğitim serisinin 5. bölümü Razor .

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde, doğrulama mantığı `Movie` modele eklenir. Doğrulama kuralları, bir Kullanıcı bir filmi oluşturduğunda veya düzenleişinizde zorlanır.

## <a name="validation"></a>Doğrulama

Yazılım geliştirmeye yönelik temel bir temel [kuru](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeon **Y** ourself") olarak adlandırılır. Razor Sayfalar, işlevlerin bir kez belirtildiği ve uygulama genelinde yansıtıldığı durumlarda geliştirmeyi teşvik eder. Kuru şu şekilde yardımcı olabilir:

* Uygulamadaki kod miktarını azaltın.
* Kodu daha az hata haline getirin ve test ve bakım yapmayı kolaylaştırın.

Sayfalar ve Entity Framework tarafından sunulan doğrulama desteği, Razor kurutma ilkesine iyi bir örnektir:

* Doğrulama kuralları, model sınıfında bildirimli olarak tek bir yerde belirtilir.
* Kurallar uygulamada her yerde zorlanır.

## <a name="add-validation-rules-to-the-movie-model"></a>Film modeline doğrulama kuralları ekleme

`DataAnnotations`Ad alanı şunları sağlar:

* Bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi.
* `[DataType]`Biçimlendirme ile yardım ve herhangi bir doğrulama sağlamayan gibi öznitelikleri biçimlendirme.

`Movie`Yerleşik `[Required]` , `[StringLength]` , `[RegularExpression]` ve doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin `[Range]` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:

* `[Required]`Ve `[MinimumLength]` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini belirtir. Hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.
* `[RegularExpression]`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır. Yukarıdaki kodda `Genre` :

  * Yalnızca harfler kullanılmalıdır.
  * İlk harfin büyük harfle olması gerekir. Boşluk, sayı ve özel karakterlere izin verilmez.

* `RegularExpression` `Rating` Şunları yapın:

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki boşlukların içindeki özel karakter ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak için başarısız olur `Genre` .

* `[Range]` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* `[StringLength]`Özniteliği bir dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en düşük uzunluğunu ayarlayabilir.
* ,,,,,,,,,,,,,,,,,, Gibi değer türleri, `decimal` `int` `float` `DateTime` `[Required]`

Yukarıdaki doğrulama kuralları tanıtım için kullanılır, bunlar bir üretim sistemi için en uygun değildir. Örneğin, önceki bir filmi yalnızca iki karakter içeren bir filmi girmeyi önler ve içinde özel karakterlere izin vermez `Genre` .

Doğrulama kurallarının ASP.NET Core tarafından otomatik olarak zorlanmasına yardımcı olur:

* Uygulamanın daha sağlam olmasına yardımcı olur.
* Veritabanına geçersiz veri kaydetme olasılığını azaltın.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Sayfalarda doğrulama hatası Kullanıcı arabirimi Razor

Uygulamayı çalıştırın ve sayfalar/Filmler ' e gidin.

**Create Yeni** bağlantıyı seçin. Formu, bazı geçersiz değerlerle doldurun. JQuery istemci tarafı doğrulaması hatayı algıladığında, bir hata iletisi görüntüler.

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Formun geçersiz bir değer içeren her alanda otomatik olarak bir doğrulama hata iletisi nasıl oluşturulduğuna dikkat edin. Hatalar, Kullanıcı JavaScript devre dışı bırakıldığında, JavaScript ve jQuery kullanılarak istemci tarafı ve sunucu tarafı ile zorlanır.

Önemli bir avantaj, ya da düzenleme sayfalarında **hiçbir** kod değişikliği gerekli değildir Create . Veri ek açıklamaları modele uygulandıktan sonra, doğrulama kullanıcı arabirimi etkinleştirilmiştir. RazorBu öğreticide oluşturulan sayfalar, model sınıfının özelliklerindeki doğrulama özniteliklerini kullanarak doğrulama kurallarını otomatik olarak çekti `Movie` . Düzenleme sayfasını kullanarak doğrulama testi, aynı doğrulama uygulanır.

Form verileri, istemci tarafı doğrulama hatası kalmayana kadar sunucuya nakledilmez. Form verilerinin aşağıdaki yaklaşımlardan bir veya daha fazlası tarafından nakledilmediğinden emin olun:

* Yöntemine bir kesme noktası koyun `OnPostAsync` . Seçerek veya kaydederek formu gönderebilirsiniz **Create** . **Save** Kesme noktası hiçbir şekilde isabet ettirilmez.
* [Fiddler aracını](https://www.telerik.com/fiddler)kullanın.
* Ağ trafiğini izlemek için tarayıcı Geliştirici Araçları ' nı kullanın.

### <a name="server-side-validation"></a>Sunucu tarafı doğrulaması

Tarayıcıda JavaScript devre dışı bırakıldığında, formun hatalarla gönderilmesi sunucuya gönderilir.

İsteğe bağlı, test sunucusu-tarafı doğrulaması:

1. Tarayıcıda JavaScript 'ı devre dışı bırakın. JavaScript tarayıcı geliştirici araçları kullanılarak devre dışı bırakılabilir. Tarayıcıda JavaScript devre dışı bırakılamaz, başka bir tarayıcı deneyin.
1. `OnPostAsync`Veya düzenleme sayfasının yönteminde bir kesme noktası ayarlayın Create .
1. Geçersiz verilerle form gönderme.
1. Model durumunun geçersiz olduğunu doğrulayın:

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
Alternatif olarak, [sunucuda istemci tarafı doğrulamayı devre dışı bırakın](xref:mvc/models/validation#disable-client-side-validation).

Aşağıdaki kod, öğreticide daha önce *Create . cshtml* sayfa scafkatın bir bölümünü gösterir. Bu, Create ve düzenleme sayfaları tarafından şu şekilde kullanılır:

* Başlangıç formunu görüntüleyin.
* Hata durumunda formu yeniden görüntüleyin.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) , [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir. [Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler. Daha fazla bilgi için bkz. [doğrulama](xref:mvc/models/validation) .

CreateVe düzenleme sayfalarında hiçbir doğrulama kuralı yoktur. Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfında belirtilmiştir. Bu doğrulama kuralları Razor , modeli düzenlediğiniz sayfalara otomatik olarak uygulanır `Movie` .

Doğrulama mantığının değişmesi gerektiğinde, yalnızca modelde yapılır. Doğrulama, uygulamanın tamamında tutarlı bir şekilde uygulanır, doğrulama mantığı tek bir yerde tanımlanır. Tek bir yerde doğrulama, kodun temiz kalmasına yardımcı olur ve bakım ve güncelleştirme işlemlerini kolaylaştırır.

## <a name="use-datatype-attributes"></a>DataType özniteliklerini kullan

Sınıfını inceleyin `Movie` . `System.ComponentModel.DataAnnotations`Ad alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar. `[DataType]` özniteliği `ReleaseDate` ve `Price` özelliklerine uygulanır.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`[DataType]`Öznitelikleri şunları sağlar:

* Görüntüleme altyapısının verileri biçimlendirmek için ipuçları.
* `<a>`URL 'ler ve e-posta için gibi öznitelikleri sağlar `<a href="mailto:EmailAddress.com">` .

`[RegularExpression]`Veri biçimini doğrulamak için özniteliğini kullanın. `[DataType]`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır. `[DataType]` öznitelikler doğrulama öznitelikleri değildir. Örnek uygulamada, yalnızca tarih ve saat olmadan görüntülenir.

`DataType`Sabit listesi,,,, ve gibi birçok veri türü sağlar `Date` `Time` `PhoneNumber` `Currency` `EmailAddress` . 

`[DataType]`Öznitelikler:

* Uygulamanın, türe özgü özellikleri otomatik olarak sağlamasına olanak sağlayabilir. Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` .
* `DataType.Date`HTML5 'i destekleyen tarayıcılarda bir tarih seçici sağlayabilir.
* HTML 5 `data-` tarayıcıların kullandığı "veri Dash" özniteliği olan HTML 5 ' i yayın.
* Herhangi bir **doğrulama sağlamamayın** .

`DataType.Date` görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucu ' a göre varsayılan biçimlere göre görüntülenir `CultureInfo` .

`[Column(TypeName = "decimal(18, 2)")]`Entity Framework Core veri ek açıklaması gerekir, bu nedenle `Price` veritabanında para birimiyle doğru şekilde eşleşebilirler. Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

`[DisplayFormat]`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode`Ayar, değer düzenlenmek üzere görüntülendiğinde biçimlendirmenin uygulanacağını belirtir. Bu davranış bazı alanlar için istenmeyebilir. Örneğin, para birimi değerlerinde, para birimi simgesi genellikle düzenleme Kullanıcı arabiriminde istenmez.

`[DisplayFormat]`Özniteliği kendisi tarafından kullanılabilir, ancak genellikle özniteliği kullanmak iyi bir fikir olabilir `[DataType]` . `[DataType]`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır. `[DataType]`Özniteliği, ile kullanılamayan aşağıdaki avantajları sağlar `[DisplayFormat]` :

* Tarayıcı HTML5 özelliklerini etkinleştirerek, örneğin bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını vb. göstermek için kullanabilir.
* Varsayılan olarak tarayıcı, verileri yerel ayarlarına göre doğru biçimi kullanarak işler.
* `[DataType]`Öznitelik, ASP.NET Core çerçevesinin verileri işlemek için doğru alan şablonunu seçmesini sağlayabilir. `DisplayFormat`Kendisi tarafından kullanılıyorsa, dize şablonunu kullanır.

**Note:** jQuery doğrulaması, `[Range]` ve özniteliğiyle çalışmaz `DateTime` . Örneğin, aşağıdaki kod, tarih belirtilen aralıkta olduğunda bile her zaman bir istemci tarafı doğrulama hatası görüntüler:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Modellerdeki sabit tarihleri dermaktan kaçınmak en iyi uygulamadır, bu nedenle `[Range]` özniteliği `DateTime` kullanılır ve önerilmez. Tarih aralıkları ve kodda belirtmek yerine sık değişikliğe tabi diğer değerler için [yapılandırma](xref:fundamentals/configuration/index) kullanın.

Aşağıdaki kod, öznitelikleri tek bir satırda birleştirmeyi gösterir:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Kullanmaya Razor başlayın Sayfalar ve EF Core](xref:data/ef-rp/intro) sayfalar ile gelişmiş EF Core işlemlerini gösterir Razor .

### <a name="apply-migrations"></a>Geçişleri Uygula

Sınıfa uygulanan Datanot açıklamaları şemayı değiştirir. Örneğin, alanına uygulanan veri ek açıklamaları `Title` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Karakterleri 60 olarak sınırlandırır.
* Bir değere izin vermez `null` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

`Movie`Tabloda Şu anda aşağıdaki şema vardır:

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

Önceki şema değişiklikleri, EF 'in özel durum oluşturmasına neden olmaz. Ancak, şemanın modelle tutarlı olması için bir geçiş oluşturun.

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu** ' nu seçin.
PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database``Up`sınıfının yöntemlerini çalıştırır `New_DataAnnotations` . `Up` yöntemini inceleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Güncelleştirilmiş `Movie` tablo aşağıdaki şemaya sahiptir:

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

SQLite için geçişler gerekli değildir.

---

### <a name="publish-to-azure"></a>Azure’da Yayımlama

Azure 'a dağıtma hakkında bilgi için bkz. [öğretici: Azure 'DA SQL veritabanı ile ASP.NET Core uygulama oluşturma](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Bu sayfalara giriş tamamlanırken teşekkürler Razor . [Kullanmaya Razor başlayın Sayfalar ve EF Core](xref:data/ef-rp/intro) Bu öğreticiye harika bir izdir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Önceki: yeni bir alan ekleyin](xref:tutorials/razor-pages/new-field)
