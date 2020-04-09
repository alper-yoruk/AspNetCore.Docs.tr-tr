---
title: ASP.NET Core'daki Razor Page'e yeni bir alan ekleme
author: rick-anderson
description: Entity Framework Core ile Bir Jilet Sayfasına nasıl yeni bir alan ekleyeceğinizi gösterir
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657817"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core'daki Razor Page'e yeni bir alan ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Otomatik olarak bir veritabanı oluşturmak için ÖNCE EF Code First'i kullanırken, Önce Kod:

* Veritabanı `__EFMigrationsHistory` şemasının oluşturulduğu model sınıflarıyla eşitlenip eşitolmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları DB ile eşit değilse, EF bir özel durum atar.

Şema/modelin eşitlenmiş olarak otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film Modeline Derecelendirme Özelliği Ekleme

*Models/Movie.cs* dosyasını açın `Rating` ve bir özellik ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulamayı oluşturun.

*Sayfaları/Filmleri/Index.cshtml'i*edin `Rating` ve alan ekleyin:

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

Aşağıdaki sayfaları güncelleyin:

* `Rating` Alanı Sil ve Ayrıntılar sayfalarına ekleyin.
* [Create.cshtml'i](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) `Rating` bir alanla güncelleştirin.
* `Rating` Alanı Edit Sayfasına ekleyin.

DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz. Veritabanını güncellemeden uygulamayı çalıştırmak bir `SqlException`atar:

`SqlException: Invalid column name 'Rating'.`

Özel `SqlException` durum, güncelleştirilmiş Film modeli sınıfının veritabanının Film tablosunun şemasından farklı olması nedeniyle kaynaklanır. (Veritabanı tablosunda `Rating` sütun yok.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Varlık Çerçevesi'nin yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar. Bu yaklaşım, geliştirme döngüsünün başlarında kullanışlıdır; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Dezavantajı veritabanında varolan verileri kaybetmek olduğunu. Bu yaklaşımı üretim veritabanında kullanmayın! Şema değişikliklerinde DB'yi bırakmak ve veritabanını test verileriyle otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur.

2. Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı verilerinizi saklamanızdır. Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.

Bu öğretici için Kod İlk Geçişler'i kullanın.

`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie` blok için bu değişikliği yapmak isteyeceksiniz.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Tamamlanan [SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)bakın.

Çözümü derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için geçiş ekleme

**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.
PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

Komut `Add-Migration` çerçevesöyler:

* `Movie` Modeli `Movie` DB şemasıyla karşılaştırın.
* DB şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.

Komut, `Update-Database` şema değişikliklerini veritabanına uygulamak ve varolan verileri korumak için çerçeveyi söyler.

<a name="ssox"></a>

DB'deki tüm kayıtları silerseniz, baş harfe çevrilen `Rating` kullanıcı DB'yi tohumlar ve alanı içerir. Bunu tarayıcıdaki silme bağlantılarıyla veya [Sql Server Object Explorer'dan](xref:tutorials/razor-pages/sql#ssox) (SSOX) yapabilirsiniz.

Başka bir seçenek veritabanını silmek ve veritabanını yeniden oluşturmak için geçişleri kullanmaktır. SSOX veritabanını silmek için:

* SSOX'taki veritabanını seçin.
* Veritabanına sağ tıklayın ve *Sil'i*seçin.
* **Varolan bağlantıları kapat'ı**işaretleyin.
* **Tamam'ı**seçin.
* [PMC'de](xref:tutorials/razor-pages/new-field#pmc)veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakma ve yeniden oluşturma

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Geçiş klasörünü silin.  Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğiniz/yönetebileceğinizi/görüntülediğinizi doğrulayın. Veritabanı tohumlu değilse, `SeedData.Initialize` yöntemde bir kesme noktası ayarlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Önceki:](xref:tutorials/razor-pages/search)
> Sonraki Arama[Ekleme: Doğrulama Ekleme](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Otomatik olarak bir veritabanı oluşturmak için ÖNCE EF Code First'i kullanırken, Önce Kod:

* Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenip eşitolmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları DB ile eşit değilse, EF bir özel durum atar.

Şema/modelin eşitlenmiş olarak otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film Modeline Derecelendirme Özelliği Ekleme

*Models/Movie.cs* dosyasını açın `Rating` ve bir özellik ekleyin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulamayı oluşturun.

*Sayfaları/Filmleri/Index.cshtml'i*edin `Rating` ve alan ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aşağıdaki sayfaları güncelleyin:

* `Rating` Alanı Sil ve Ayrıntılar sayfalarına ekleyin.
* [Create.cshtml'i](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) `Rating` bir alanla güncelleştirin.
* `Rating` Alanı Edit Sayfasına ekleyin.

DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz. Şimdi çalıştırılırsa, uygulama atar: `SqlException`

`SqlException: Invalid column name 'Rating'.`

Bu hata, güncelleştirilmiş Film modeli sınıfının veritabanının Film tablosunun şemasından farklı olması nedeniyle kaynaklanır. (Veritabanı tablosunda `Rating` sütun yok.)

Hatayı çözmek için birkaç yaklaşım vardır:

1. Varlık Çerçevesi'nin yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar. Bu yaklaşım, geliştirme döngüsünün başlarında kullanışlıdır; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Dezavantajı veritabanında varolan verileri kaybetmek olduğunu. Bu yaklaşımı üretim veritabanında kullanmayın! Şema değişikliklerinde DB'yi bırakmak ve veritabanını test verileriyle otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur.

2. Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı verilerinizi saklamanızdır. Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.

3. Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.

Bu öğretici için Kod İlk Geçişler'i kullanın.

`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie` blok için bu değişikliği yapmak isteyeceksiniz.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Tamamlanan [SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.

Çözümü derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için geçiş ekleme

**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.
PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

Komut `Add-Migration` çerçevesöyler:

* `Movie` Modeli `Movie` DB şemasıyla karşılaştırın.
* DB şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.

Komut, `Update-Database` şema değişikliklerini veritabanına uygulamak için çerçeveyi söyler.

<a name="ssox"></a>

DB'deki tüm kayıtları silerseniz, baş harfe çevrilen `Rating` kullanıcı DB'yi tohumlar ve alanı içerir. Bunu tarayıcıdaki silme bağlantılarıyla veya [Sql Server Object Explorer'dan](xref:tutorials/razor-pages/sql#ssox) (SSOX) yapabilirsiniz.

Başka bir seçenek veritabanını silmek ve veritabanını yeniden oluşturmak için geçişleri kullanmaktır. SSOX veritabanını silmek için:

* SSOX'taki veritabanını seçin.
* Veritabanına sağ tıklayın ve *Sil'i*seçin.
* **Varolan bağlantıları kapat'ı**işaretleyin.
* **Tamam'ı**seçin.
* [PMC'de](xref:tutorials/razor-pages/new-field#pmc)veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakma ve yeniden oluşturma

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Veritabanını silin ve veritabanını yeniden oluşturmak için geçişleri kullanın. Veritabanını silmek için veritabanı dosyasını silin (*MvcMovie.db*). Sonra komutu çalıştırın: `ef database update`

```dotnetcli
dotnet ef database update
```

---

Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğiniz/yönetebileceğinizi/görüntülediğinizi doğrulayın. Veritabanı tohumlu değilse, `SeedData.Initialize` yöntemde bir kesme noktası ayarlayın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Önceki:](xref:tutorials/razor-pages/search)
> Sonraki Arama[Ekleme: Doğrulama Ekleme](xref:tutorials/razor-pages/validation)

::: moniker-end
