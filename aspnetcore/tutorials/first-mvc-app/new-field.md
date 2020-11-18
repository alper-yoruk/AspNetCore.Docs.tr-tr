---
title: 8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin 8. bölümü.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: ce119d79bc96f01803b63c715332ec3d287473ff
ms.sourcegitcommit: df808efa68574dd674f1985aa9d03f4f5fab883f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94851188"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a>8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alanı veritabanına geçirin.

EF Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Code First:

* Veritabanının şemasını izlemek için veritabanına tablo ekler.
* Veritabanının oluşturulduğu model sınıflarıyla eşitlenmiş olduğunu doğrular. Bunlar eşitlenmiş değilse EF bir özel durum oluşturur. Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="add-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleyin

`Rating` *Modeller/film. cs*'ye bir özellik ekleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

Uygulamayı oluşturma

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

 Ctrl+Shift+B

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Komut ⌘ + B

------

Sınıfa yeni bir alan eklediğiniz için `Movie` Özellik bağlama listesini güncelleştirmeniz gerekir, bu nedenle bu yeni özellik dahil edilecek. *MoviesController.cs* içinde, `[Bind]` `Create` `Edit` özelliği dahil etmek için hem hem de eylem yöntemlerinin özniteliğini güncelleştirin `Rating` :

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Yeni özelliği tarayıcı görünümünde görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin `Rating` .

*/Views/movies/Index.cshtml* dosyasını düzenleyin ve bir alan ekleyin `Rating` :

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-63)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-63)]

::: moniker-end

*/Views/movies/Create.cshtml* ile bir alanı güncelleştirin `Rating` .

# <a name="visual-studio--visual-studio-for-mac"></a>[Visual Studio/Mac için Visual Studio](#tab/visual-studio+visual-studio-mac)

Önceki "form grubunu kopyalayabilir/yapıştırabilir" ve IntelliSense 'in alanları güncelleştirmenize yardımcı olmasına izin verebilirsiniz. IntelliSense, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile birlikte çalışmaktadır.

![Geliştirici, görünümün ikinci Label öğesinde için ASP-for öznitelik değeri için R harfini yazmıştır. Bir IntelliSense bağlamsal menüsü, listede otomatik olarak vurgulanmış olan derecelendirme dahil olmak üzere kullanılabilir alanları gösterir. Geliştirici alana tıkladığında veya klavyede ENTER tuşuna bastığında, değer derecelendirme olarak ayarlanır.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Kalan şablonları güncelleştirin.

`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her biri için bu değişikliği yapmak isteyeceksiniz `new Movie` .

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Şimdi çalıştırıldığında, şunlar `SqlException` oluşur:

`SqlException: Invalid column name 'Rating'.`

Bu hata, güncelleştirilmiş film modeli sınıfı varolan veritabanının film tablosunun şemasından farklı olduğu için oluşur. ( `Rating` Veritabanı tablosunda sütun yok.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Entity Framework yeni model sınıfı şemasına göre otomatik olarak veritabanını bırakıp yeniden oluşturmayı sağlayabilirsiniz. Bu yaklaşım, bir test veritabanı üzerinde etkin geliştirme yaparken geliştirme döngüsünün başlarında çok daha kolay. modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır. Bunun yanında, bu yaklaşımı bir üretim veritabanında kullanmak istemezsiniz, ancak bu, veritabanında var olan verileri kaybetmeniz olur. Bir veritabanının test verileriyle otomatik olarak çekirdeği oluşturmak için bir başlatıcı kullanılması, genellikle bir uygulama geliştirmenin üretken bir yoludur. Bu, erken geliştirme ve SQLite kullanılırken iyi bir yaklaşımdır.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanılır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.

  ![PMC menüsü](adding-model/_static/pmc.png)

PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

Bu `Add-Migration` komut, geçiş çerçevesinin geçerli `Movie` DB şemasıyla geçerli modeli INCELEMESINI `Movie` ve veritabanını yeni modele geçirmek için gerekli kodu oluşturmasını söyler.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

VERITABANıNDAKI tüm kayıtlar silinirse, Initialize yöntemi VERITABANıNı temel alır ve `Rating` alanını içerir.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Veritabanını ve önceki geçişini silip, veritabanını yeniden oluşturmak için geçişleri kullanın:

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

`dotnet ef migrations remove` Son geçişi kaldırır. Birden fazla geçiş varsa, geçişler klasörünü silin.

---
<!-- End of VS tabs -->

Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz, düzenleyebileceğiniz ve görüntüleyebilen bir doğrulama yapabilirsiniz `Rating` .

> [!div class="step-by-step"]
> [Önceki](search.md) 
>  [Sonraki](validation.md)
