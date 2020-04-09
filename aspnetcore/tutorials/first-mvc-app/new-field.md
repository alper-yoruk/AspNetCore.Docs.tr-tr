---
title: ASP.NET Core MVC uygulamasına yeni bir alan ekleme
author: rick-anderson
description: Bir modele yeni bir alan eklemek ve bu değişikliği veritabanına geçirmek için Varlık Çerçeve Kodu İlk Geçişleri'ni nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662906"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına yeni bir alan ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alanı veritabanına geçirin.

Ef Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Önce Kod:

* Veritabanının şemasını izlemek için veritabanına bir tablo ekler.
* Veritabanının oluşturulduğu model sınıflarıyla eşit olduğunu doğrular. Eşitlenmemişlerse, EF bir özel durum oluşturur. Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="add-a-rating-property-to-the-movie-model"></a>Film Modeline Derecelendirme Özelliği Ekleme

`Rating` *Modellere/Movie.cs'ye*özellik ekle :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulama oluşturma

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut + B

------

`Movie` Sınıfa yeni bir alan eklediğiniz için, bu yeni özelliğin eklenmesi için bağlayıcı beyaz listeyi güncelleştirmeniz gerekir. *MoviesController.cs,* hem `[Bind]` özellik `Create` hem de `Edit` eylem yöntemleri için `Rating` özniteliği güncelleştirmeözelliği içerecek şekilde:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Tarayıcı görünümünde yeni `Rating` özelliği görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin.

*/Views/Movies/Index.cshtml* dosyasını edin `Rating` ve bir alan ekleyin:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

*/Views/Movies/Create.cshtml'i* bir `Rating` alanla güncelleştirin.

# <a name="visual-studio--visual-studio-for-mac"></a>[Mac için Visual Studio / Visual Studio](#tab/visual-studio+visual-studio-mac)

Önceki "form grubunu" kopyalayabilir/yapıştırabilir ve intelliSense'in alanları güncelleştirmenize yardımcı olur. IntelliSense [Tag Helpers](xref:mvc/views/tag-helpers/intro)ile çalışır.

![Geliştirici, görünümün ikinci etiket öğesine asp-for öznitelik değeri için R harfini yazdı. Listede otomatik olarak vurgulanan Derecelendirme de dahil olmak üzere kullanılabilir alanları gösteren bir Intellisense bağlamsal menü belirlemiştir. Geliştirici alanı tıklattığında veya klavyede Enter tuşuna bastığında, değer Derecelendirme olarak ayarlanır.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Kalan şablonları güncelleştirin.

`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie`biri için bu değişikliği yapmak isteyeceksiniz.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz. Şimdi çalıştırılırsa, aşağıdakiler `SqlException` atılır:

`SqlException: Invalid column name 'Rating'.`

Güncelleştirilmiş Film modeli sınıfı varolan veritabanının Film tablosunun şemasından farklı olduğundan bu hata oluşur. (Veritabanı tablosunda `Rating` sütun yok.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Varlık Çerçevesi'nin yeni model sınıfı şemasına dayalı veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar. Bu yaklaşım, bir test veritabanında etkin geliştirme yaparken geliştirme döngüsünün başlarında çok uygundur; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Dezavantajı olsa da, veritabanında mevcut verileri kaybetmek olduğunu - bu yüzden bir üretim veritabanında bu yaklaşımı kullanmak istemiyorum! Test verileriyle bir veritabanını otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur. Bu erken gelişim için iyi bir yaklaşımdır ve SQLite kullanırken.

2. Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı verilerinizi saklamanızdır. Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.

Bu öğretici için, Kod İlk Geçişler kullanılır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.

  ![PMC menüsü](adding-model/_static/pmc.png)

PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

Komut, `Add-Migration` geçerli `Movie` `Movie` DB şeması yla geçerli modeli incelemesi ve DB'yi yeni modele geçirmek için gerekli kodu oluşturmasını geçiş çerçevesine söyler.

"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.

DB'deki tüm kayıtlar silinirse, başharf yöntemi DB'yi `Rating` tohumlar ve alanı içerir.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Veritabanını silin ve veritabanını yeniden oluşturmak için geçişleri kullanın. Veritabanını silmek için veritabanı dosyasını silin (*MvcMovie.db*). Sonra komutu çalıştırın: `ef database update`

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğinizi, edinebileceğinizi ve görüntülediğinizi doğrulayın. Uygulamayı güncelleyin:

* `Rating` Alanı `Edit`, , `Details`ve `Delete` görünüm şablonlarına ekleyin.
* 'nin edit eylem yöntemindeki `MoviesController`bağlamayı güncelleştirin.

> [!div class="step-by-step"]
> [Önceki](search.md)
> [Sonraki](validation.md)
