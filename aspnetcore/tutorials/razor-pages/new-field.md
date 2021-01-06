---
title: 7. bölüm, yeni bir alan ekleyin
author: rick-anderson
description: Sayfalardaki eğitim serisinin Bölüm 7 Razor .
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486167"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a>Bölüm 7, ASP.NET Core bir sayfaya yeni bir alan ekleyin Razor

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:

* [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.

Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleme

1. *Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Uygulamayı oluşturun.

1. *Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Aşağıdaki sayfaları güncelleştirin:
   1. `Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.
   1. [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.
   1. `Rating`Alanı düzenleme sayfasına ekleyin.

Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :

`SqlException: Invalid column name 'Rating'.`

`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır. `Rating`Veritabanı tablosunda sütun yok.

Hatayı çözmek için birkaç yaklaşım vardır:

1. Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework. Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Downsıde, veritabanında var olan verileri kaybetmeniz. Bu yaklaşımı bir üretim veritabanında kullanmayın! Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerin tutulması. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.

Çözümü derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için bir geçiş ekleyin

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
2. PMC 'de aşağıdaki komutları girin:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration`Komut, çerçeveye şunları belirtir:

* `Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.
* Veritabanı şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.

<a name="ssox"></a>

Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir. Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.

Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır. SSOX 'te veritabanını silmek için:

1. SSOX 'te veritabanını seçin.
1. Veritabanına sağ tıklayın ve **Sil**' i seçin.
1. **Mevcut bağlantıları kapat**' a bakın.
1. **Tamam**’ı seçin.
1. [PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

> [!NOTE]
> Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanırsınız. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)

1. Geçiş klasörünü silin.  

1. Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` . Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:

* [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.

Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleme

1. *Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. Uygulamayı oluşturun.

1. *Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. Aşağıdaki sayfaları güncelleştirin:
   1. `Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.
   1. [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.
   1. `Rating`Alanı düzenleme sayfasına ekleyin.

Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :

`SqlException: Invalid column name 'Rating'.`

`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır. `Rating`Veritabanı tablosunda sütun yok.

Hatayı çözmek için birkaç yaklaşım vardır:

1. Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework. Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Downsıde, veritabanında var olan verileri kaybetmeniz. Bu yaklaşımı bir üretim veritabanında kullanmayın! Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerin tutulması. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.

Çözümü derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için bir geçiş ekleyin

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
2. PMC 'de aşağıdaki komutları girin:

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration`Komut, çerçeveye şunları belirtir:

* `Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.
* Veritabanı şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.

<a name="ssox"></a>

Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir. Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.

Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır. SSOX 'te veritabanını silmek için:

* SSOX 'te veritabanını seçin.
* Veritabanına sağ tıklayın ve **Sil**' i seçin.
* **Mevcut bağlantıları kapat**' a bakın.
* **Tamam**’ı seçin.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

> [!NOTE]
> Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)

1. Geçiş klasörünü silin.  

1. Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` . Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:

* Modele yeni bir alan ekleyin.
* Yeni alan şeması değişikliğini veritabanına geçirin.

Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:

* [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.
* Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.

Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Film modeline bir derecelendirme özelliği ekleme

*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Uygulamayı oluşturun.

*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

Aşağıdaki sayfaları güncelleştirin:

* `Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.
* [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.
* `Rating`Alanı düzenleme sayfasına ekleyin.

Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz. Uygulama Şu anda çalıştırıldığında, uygulama şunu oluşturur `SqlException` :

`SqlException: Invalid column name 'Rating'.`

Bu hata, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır. `Rating`Veritabanı tablosunda sütun yok.

Hatayı çözmek için birkaç yaklaşım vardır:

1. Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework. Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar. Downsıde, veritabanında var olan verileri kaybetmeniz. Bu yaklaşımı bir üretim veritabanında kullanmayın! Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.

2. Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin. Bu yaklaşımın avantajı, verilerin tutulması. Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.

3. Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.

Bu öğretici için Code First Migrations kullanın.

`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin. Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.

Çözümü derleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Derecelendirme alanı için bir geçiş ekleyin

**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.
PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration`Komut, çerçeveye şunları belirtir:

* `Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.
* Veritabanı şemasını yeni modele geçirmek için kod oluşturun.

"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır. Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.

`Update-Database`Komutu, çerçeveye şema değişikliklerini veritabanına uygulamasını söyler.

<a name="ssox"></a>

Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir. Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.

Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır. SSOX 'te veritabanını silmek için:

* SSOX 'te veritabanını seçin.
* Veritabanına sağ tıklayın ve **Sil**' i seçin.
* **Mevcut bağlantıları kapat**' a bakın.
* **Tamam**’ı seçin.
* [PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>Veritabanını bırakıp yeniden oluşturun

> [!NOTE]
> Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
>
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)

Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun. Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin. Ardından şu `ef database update` komutu çalıştırın:

```dotnetcli
dotnet ef database update
```

---

Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` . Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)

::: moniker-end
