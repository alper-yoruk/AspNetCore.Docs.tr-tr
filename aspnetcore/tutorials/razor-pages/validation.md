---
title: 8. bölüm, ASP.NET Core sayfasına doğrulama ekleme Razor
author: rick-anderson
description: Sayfalardaki eğitim serisinin 5. bölümü Razor .
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 86c523c69d3ee85f56bf1a51719a0bd93cbe97fc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633558"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a>8. bölüm, ASP.NET Core sayfasına doğrulama ekleme Razor

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde, doğrulama mantığı `Movie` modele eklenir. Doğrulama kuralları, bir Kullanıcı bir filmi oluşturduğunda veya düzenleişinizde zorlanır.

## <a name="validation"></a>Doğrulama

Yazılım geliştirmeye yönelik temel bir temel [kuru](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeon **Y**ourself") olarak adlandırılır. Razor Sayfalar, işlevlerin bir kez belirtildiği ve uygulama genelinde yansıtıldığı durumlarda geliştirmeyi teşvik eder. Kuru şu şekilde yardımcı olabilir:

* Uygulamadaki kod miktarını azaltın.
* Kodu daha az hata haline getirin ve test ve bakım yapmayı kolaylaştırın.

Sayfalar ve Entity Framework tarafından sunulan doğrulama desteği, Razor kurutma ilkesine iyi bir örnektir. Doğrulama kuralları tek bir yerde (model sınıfında) bildirimli olarak belirtilir ve kurallar uygulamada her yerde zorlanır.

## <a name="add-validation-rules-to-the-movie-model"></a>Film modeline doğrulama kuralları ekleme

Dataaçıklamalarda ad alanı, bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi sağlar. Dataaçıklamalarda, `DataType` biçimlendirme ile ilgili yardım ve herhangi bir doğrulama sağlamayan gibi biçimlendirme öznitelikleri de bulunur.

`Movie`Yerleşik `Required` , `StringLength` , `RegularExpression` ve doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin `Range` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak istediğiniz davranışı belirtir:

* `Required`Ve `MinimumLength` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini belirtir; ancak hiçbir şey, bir kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.
* `RegularExpression`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır. Yukarıdaki kodda, "tarz":

  * Yalnızca harfler kullanılmalıdır.
  * İlk harfin büyük harfle olması gerekir. Boşluk, sayı ve özel karakterlere izin verilmez.

* `RegularExpression`"Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki boşlukların içindeki özel karakter ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "tarz" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* `StringLength`Özniteliği, bir dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en düşük uzunluğunu ayarlamanıza olanak sağlar.
* Değer türleri (örneğin,,, `decimal` `int` ), doğal olarak `float` `DateTime` gereklidir ve özniteliğe gerek kalmaz `[Required]` .

Doğrulama kurallarının otomatik olarak uygulanmasını ASP.NET Core uygulamanızın daha sağlam olmasına yardımcı olur. Ayrıca, bir şeyi doğrulamayı unutmanızı ve veritabanına yanlışlıkla veri vermemesini de sağlar.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Sayfalarda doğrulama hatası Kullanıcı arabirimi Razor

Uygulamayı çalıştırın ve sayfalar/Filmler ' e gidin.

**Yeni oluştur** bağlantısını seçin. Formu, bazı geçersiz değerlerle doldurun. JQuery istemci tarafı doğrulaması hatayı algıladığında, bir hata iletisi görüntüler.

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Formun geçersiz bir değer içeren her alanda otomatik olarak bir doğrulama hata iletisi nasıl oluşturulduğuna dikkat edin. Hatalar hem istemci tarafında (JavaScript ve jQuery kullanılarak) hem de sunucu tarafında (bir Kullanıcı JavaScript devre dışı bırakıldığında) zorlanır.

Önemli bir avantaj, oluşturma veya düzenleme sayfalarında **hiçbir** kod değişikliği gerekli değildir. Veri ek açıklamaları modele uygulandıktan sonra, doğrulama kullanıcı arabirimi etkinleştirilmiştir. RazorBu öğreticide oluşturulan sayfalar otomatik olarak doğrulama kurallarını (model sınıfının özelliklerinde doğrulama özniteliklerini kullanarak `Movie` ) alır. Düzenleme sayfasını kullanarak doğrulama testi, aynı doğrulama uygulanır.

Form verileri, istemci tarafı doğrulama hatası kalmayana kadar sunucuya nakledilmez. Form verilerinin aşağıdaki yaklaşımlardan bir veya daha fazlası tarafından nakledilmediğinden emin olun:

* Yöntemine bir kesme noktası koyun `OnPostAsync` . Formu gönder ( **Oluştur** veya **Kaydet**' i seçin). Kesme noktası hiçbir şekilde isabet ettirilmez.
* [Fiddler aracını](https://www.telerik.com/fiddler)kullanın.
* Ağ trafiğini izlemek için tarayıcı Geliştirici Araçları ' nı kullanın.

### <a name="server-side-validation"></a>Sunucu tarafı doğrulaması

Tarayıcıda JavaScript devre dışı bırakıldığında, formun hatalarla gönderilmesi sunucuya gönderilir.

İsteğe bağlı, test sunucusu-tarafı doğrulaması:

* Tarayıcıda JavaScript 'ı devre dışı bırakın. Tarayıcının geliştirici araçlarını kullanarak JavaScript 'ı devre dışı bırakabilirsiniz. Tarayıcıda JavaScript 'ı devre dışı bırakadıysanız başka bir tarayıcı deneyin.
* `OnPostAsync`Oluşturma veya düzenleme sayfasının yönteminde bir kesme noktası ayarlayın.
* Geçersiz verilerle form gönderme.
* Model durumunun geçersiz olduğunu doğrulayın:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
Alternatif olarak, [sunucuda istemci tarafı doğrulamayı devre dışı](xref:mvc/models/validation#disable-client-side-validation)bırakabilirsiniz.

Aşağıdaki kod, öğreticide daha önce *Create. cshtml* sayfa scafkatın bir bölümünü gösterir. İlk formu görüntülemek ve bir hata durumunda formu yeniden görüntülemek için sayfa oluşturma ve düzenleme sayfaları tarafından kullanılır.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) , [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir. [Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler. Daha fazla bilgi için bkz. [doğrulama](xref:mvc/models/validation) .

Oluşturma ve düzenleme sayfalarında hiçbir doğrulama kuralı yoktur. Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfında belirtilmiştir. Bu doğrulama kuralları Razor , modeli düzenlediğiniz sayfalara otomatik olarak uygulanır `Movie` .

Doğrulama mantığının değişmesi gerektiğinde, yalnızca modelde yapılır. Doğrulama, uygulamanın tamamında tutarlı bir şekilde uygulanır (doğrulama mantığı tek bir yerde tanımlanır). Tek bir yerde doğrulama, kodun temiz kalmasına yardımcı olur ve bakım ve güncelleştirme işlemlerini kolaylaştırır.

## <a name="using-datatype-attributes"></a>DataType özniteliklerini kullanma

Sınıfını inceleyin `Movie` . `System.ComponentModel.DataAnnotations`Ad alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar. `DataType` özniteliği `ReleaseDate` ve `Price` özelliklerine uygulanır.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType`Öznitelikler yalnızca görünüm altyapısının verileri biçimlendirmek için ipuçları sağlar (ve `<a>` URL 'ler ve e-posta için gibi öznitelikleri sağlar `<a href="mailto:EmailAddress.com">` ). `RegularExpression`Veri biçimini doğrulamak için özniteliğini kullanın. `DataType`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır. `DataType` Öznitelikler, doğrulama öznitelikleri değildir. Örnek uygulamada, yalnızca tarih ve saat olmadan görüntülenir.

`DataType`Sabit listesi, tarih, saat, PhoneNumber, para birimi, Emaadresi gibi birçok veri türünü sağlar. `DataType`Özniteliği Ayrıca uygulamanın türe özgü özellikleri otomatik olarak sağlamasını da sağlayabilir. Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` . HTML5 'i destekleyen tarayıcılarda için bir tarih seçici sağlanmış olabilir `DataType.Date` . Öznitelikler HTML 5 `DataType` `data-` TARAYıCıLARıNıN kullandığı HTML 5 (bir veri Dash) özniteliklerini yayar. `DataType`Öznitelikler herhangi bir **not** doğrulama sağlamaz.

`DataType.Date` görüntülenen tarihin biçimini belirtmez. Varsayılan olarak, veri alanı sunucu ' a göre varsayılan biçimlere göre görüntülenir `CultureInfo` .

`[Column(TypeName = "decimal(18, 2)")]`Entity Framework Core veri ek açıklaması gerekir, bu nedenle `Price` veritabanında para birimiyle doğru şekilde eşleşebilirler. Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).

`DisplayFormat`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode`Ayar, değer düzenlenmek üzere görüntülendiğinde biçimlendirmenin uygulanacağını belirtir. Bazı alanlar için bu davranışı istemiyor olabilirsiniz. Örneğin, para birimi değerlerinde, büyük olasılıkla düzenleme kullanıcı arabirimindeki para birimi sembolünü istemezsiniz.

`DisplayFormat`Özniteliği kendisi tarafından kullanılabilir, ancak genellikle özniteliği kullanmak iyi bir fikir olabilir `DataType` . `DataType`Özniteliği, bir ekranda nasıl işlenirim aksine, verilerin semantiğini alır ve DisplayFormat ile elde olmadığınız avantajları sağlar:

* Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin, bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını vb. göstermek için)
* Varsayılan olarak tarayıcı, verileri yerel ayarınızı temel alarak doğru biçimi kullanarak işleyebilir.
* `DataType`Öznitelik, ASP.NET Core çerçevesinin verileri işlemek için doğru alan şablonunu seçmesini sağlayabilir. `DisplayFormat`Kendisi tarafından kullanılıyorsa, dize şablonunu kullanır.

**Note:** jQuery doğrulaması, `Range` ve özniteliğiyle çalışmaz `DateTime` . Örneğin, aşağıdaki kod, tarih belirtilen aralıkta olduğunda bile her zaman bir istemci tarafı doğrulama hatası görüntüler:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Genellikle, modellerinizde sabit tarihleri derlemek iyi bir uygulamadır, bu nedenle `Range` özniteliğini kullanarak ve `DateTime` önerilmez.

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

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
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

Azure 'a dağıtma hakkında bilgi için bkz. [öğretici: Azure 'DA SQL veritabanı ile ASP.NET Core uygulama oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).

Bu sayfalara giriş tamamlanırken teşekkürler Razor . [Kullanmaya Razor başlayın Sayfalar ve EF Core](xref:data/ef-rp/intro) Bu öğreticiye harika bir izdir.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Bu öğreticinin YouTube sürümü](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Önceki: yeni bir alan ekleme](xref:tutorials/razor-pages/new-field)
