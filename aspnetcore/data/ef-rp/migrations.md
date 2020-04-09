---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Göçler - 8'de 4
author: rick-anderson
description: Bu eğitimde, ASP.NET Core MVC uygulamasında veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlarsınız.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656536"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Göçler - 8'de 4

Yazar: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve Rick [Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini tanıtır.

Yeni bir uygulama geliştirildiğinde, veri modeli sık sık değişir. Model her değiştiğinde, model veritabanıyla eşitlenir. Bu öğretici seri, Varlık Çerçevesi'ni yoksa veritabanını oluşturmak üzere yapılandırarak başlatılır. Veri modeli her değiştiğinde, veritabanını bırakmanız gerekir. Uygulama bir sonraki çalıştığında, veritabanını yeni veri modeliyle eşleşecek şekilde `EnsureCreated` yeniden oluşturur. Sınıf `DbInitializer` daha sonra yeni veritabanı tohum çalışır.

Veritabanını veri modeliyle eşit tutma yaklaşımı, uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalışırken, genellikle korunması gereken verileri depolamaktır. Uygulama, her değişiklik yapıldığında (yeni bir sütun ekleme gibi) bir test veritabanıyla başalamaz. EF Core Geçişler özelliği, EF Core'un yeni bir veritabanı oluşturmak yerine veritabanı şemasını güncelleştirmesini sağlayarak bu sorunu çözer.

Veri modeli değiştiğinde veritabanını bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve varolan verileri korur.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Veritabanını bırak

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanını silmek veya Paket Yöneticisi Konsolu'nda (PMC) **Package Manager Console** aşağıdaki komutu çalıştırmak için SQL Server **Object Explorer'ı** (SSOX) kullanın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* EF CLI'yi yüklemek için aşağıdaki komut u komutisteminde çalıştırın:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* Komut isteminde, proje klasörüne gidin. Proje klasörü *ContosoUniversity.csproj* dosyasını içerir.

* *CU.db* dosyasını silin veya aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>İlk geçiş oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC'de aşağıdaki komutları çalıştırın:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut isteminin proje klasöründe olduğundan emin olun ve aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Yukarı ve Aşağı yöntemleri

EF Core `migrations add` komutu veritabanını oluşturmak için kod oluşturdu. Bu geçişler kodu *Geçişler\<zaman damgası>_InitialCreate.cs* dosyasında dır. `InitialCreate` Sınıfın `Up` yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur. Yöntem, `Down` aşağıdaki örnekte gösterildiği gibi, bunları siler:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Önceki kod ilk geçiş içindir. Kod:

* komutu `migrations add InitialCreate` tarafından oluşturuldu. 
* Komut tarafından `database update` yürütülür.
* Veritabanı bağlam sınıfı tarafından belirtilen veri modeli için bir veritabanı oluşturur.

Dosya adı için geçiş adı parametresi (örnekte "InitialCreate" ) kullanılır. Geçiş adı geçerli bir dosya adı olabilir. Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, bir bölüm tablosu eklenen bir geçiş "AddDepartmentTable" olarak adlandırılabilir.

## <a name="the-migrations-history-table"></a>Geçişler geçmişi tablosu

* Veritabanını incelemek için SSOX'ı veya SQLite aracınızı kullanın.
* Bir `__EFMigrationsHistory` tablo eklenmesine dikkat edin. Tablo, `__EFMigrationsHistory` veritabanına hangi geçişlerin uygulandığını izler.
* Tablodaki verileri `__EFMigrationsHistory` görüntüleyin. İlk geçiş için bir satır gösterir.

## <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veri modelinin *anlık görüntüsünü* oluşturur. Geçiş eklediğinizde, EF geçerli veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.

Anlık görüntü dosyası veri modelinin durumunu izlediğinden, dosyayı `<timestamp>_<migrationname>.cs` silerek geçişi silemezsiniz. En son geçişi geri almak için komutu `migrations remove` kullanmanız gerekir. Bu komut geçişi siler ve anlık görüntünün doğru şekilde sıfırlanın. Daha fazla bilgi için [dotnet ef geçişleri kaldırın](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)bakın.

## <a name="remove-ensurecreated"></a>EnsureCreated'ı kaldırma

Bu öğretici serisi `EnsureCreated`kullanılarak başladı. `EnsureCreated`geçişler tarih tablosu oluşturmaz ve bu nedenle geçişlerle kullanılamaz. Veritabanının sık sık bırakıldığı ve yeniden oluşturulduğu yerde test veya hızlı prototipleme için tasarlanmıştır.

Bu noktadan itibaren, öğreticiler geçişleri kullanır.

*Data/DBInitializer.cs*olarak, aşağıdaki satırı dışarı yorum:

```csharp
context.Database.EnsureCreated();
```
Uygulamayı çalıştırın ve veritabanının tohumlu olduğunu doğrulayın.

## <a name="applying-migrations-in-production"></a>Üretimde göçlerin uygulanması

Üretim uygulamalarının uygulama nın başlatılmasında [Database.Migrate'i](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **aramamalarını** öneririz. `Migrate`bir sunucu çiftliğine dağıtılan bir uygulamadan çağrılmamalıdır. Uygulama birden çok sunucu örneğine ölçeklendirildiyse, veritabanı şema güncelleştirmelerinin birden çok sunucudan gerçekleşmemesini veya okuma/yazma erişimiyle çakışmamasını sağlamak zordur.

Veritabanı geçişi dağıtımın bir parçası olarak ve kontrollü bir şekilde yapılmalıdır. Üretim veritabanı geçiş yaklaşımları şunlardır:

* SQL komut dosyaları oluşturmak için geçişleri kullanma ve dağıtımda SQL komut dosyalarını kullanma.
* Kontrollü `dotnet ef database update` bir ortamdan kaçıyor.

## <a name="troubleshooting"></a>Sorun giderme

Uygulama SQL Server LocalDB kullanıyorsa ve aşağıdaki özel durumu görüntülerse:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Çözüm bir komut `dotnet ef database update` istemi çalıştırmak için olabilir.

### <a name="additional-resources"></a>Ek kaynaklar

* [EF Çekirdek CLI](/ef/core/miscellaneous/cli/dotnet).
* [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğretici, varlık özellikleri ve yeni varlıklar ekleyerek veri modelini oluşturur.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/sort-filter-page)
> [Sonraki öğretici](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği kullanılmıştır.

Çözemediğiniz sorunlarla karşılaştıysanız, tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

Yeni bir uygulama geliştirildiğinde, veri modeli sık sık değişir. Model her değiştiğinde, model veritabanıyla eşitlenir. Bu öğretici, varlık çerçevesi varsa veritabanı oluşturmak için yapılandırılarak başlatılır. Veri modeli her değiştiğinde:

* DB düştü.
* EF, modelle eşleşen yeni bir model oluşturur.
* Uygulama, DB'yi test verileriyle tohumlar.

DB'yi veri modeliyle eşit tutma yaklaşımı, uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalışırken, genellikle korunması gereken verileri depolamaktır. Uygulama, her değişiklik yapıldığında (yeni bir sütun ekleme gibi) bir test DB ile başalamaz. EF Core Migrations özelliği, EF Core'un yeni bir DB oluşturmak yerine DB şemasını güncelleştirmesini sağlayarak bu sorunu çözer.

Veri modeli değiştiğinde DB'yi bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve varolan verileri korur.

## <a name="drop-the-database"></a>Veritabanını bırak

**SQL Server Object Explorer** (SSOX) veya komutu `database drop` kullanın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

Yardım `Get-Help about_EntityFrameworkCore` bilgileri almak için PMC'den çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Komut penceresini açın ve proje klasörüne gidin. Proje klasörü *Startup.cs* dosyasını içerir.

Komut penceresine aşağıdakileri girin:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>İlk geçiş oluşturma ve DB'yi güncelleştirme

Projeyi oluşturun ve ilk geçişi oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Yukarı ve Aşağı yöntemlerini inceleyin

EF Core `migrations add` komutu DB oluşturmak için kod oluşturdu. Bu geçişler kodu *Geçişler\<zaman damgası>_InitialCreate.cs* dosyasında dır. `InitialCreate` Sınıfın `Up` yöntemi, veri modeli varlık kümelerine karşılık gelen DB tablolarını oluşturur. Yöntem, `Down` aşağıdaki örnekte gösterildiği gibi, bunları siler:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak için yöntemi çağırır. Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemi çağırır.

Önceki kod ilk geçiş içindir. Bu kod `migrations add InitialCreate` komut çalıştırıldığında oluşturuldu. Dosya adı için geçiş adı parametresi (örnekte "InitialCreate" ) kullanılır. Geçiş adı geçerli bir dosya adı olabilir. Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, bir bölüm tablosu eklenen bir geçiş "AddDepartmentTable" olarak adlandırılabilir.

İlk geçiş oluşturulur ve DB varsa:

* DB oluşturma kodu oluşturulur.
* DB zaten veri modeliyle eşleştiğinden, DB oluşturma kodunun çalışmasına gerek yoktur. DB oluşturma kodu çalıştırılırsa, DB zaten veri modeliyle eşleştiğinden herhangi bir değişiklik yapmaz.

Uygulama yeni bir ortama dağıtıldığında, DB oluşturma kodunun DB'yi oluşturmak için çalıştırılması gerekir.

Daha önce DB düştü ve yok, bu nedenle geçişler yeni DB oluşturur.

### <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur. Geçiş eklediğinizde, EF veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.

Geçişi silmek için aşağıdaki komutu kullanın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kaldırma-Geçiş

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Daha fazla bilgi için [dotnet ef geçişleri kaldırın](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)bakın.

---

Geçişleri kaldır komutu geçişi siler ve anlık görüntünün doğru şekilde sıfırlamasını sağlar.

### <a name="remove-ensurecreated-and-test-the-app"></a>EnsureCreated'ı kaldırın ve uygulamayı test edin

Erken gelişim `EnsureCreated` için, kullanılmıştır. Bu öğreticide, geçişler kullanılır. `EnsureCreated`aşağıdaki sınırlamalar vardır:

* Geçişleri atlar ve DB ve şema oluşturur.
* Geçiş tablosu oluşturmaz.
* Geçişlerde *kullanılamaz.*
* DB'nin sık sık bırakıldığı ve yeniden oluşturulduğu test veya hızlı prototipleme için tasarlanmıştır.

Kaldırın: `EnsureCreated`

```csharp
context.Database.EnsureCreated();
```

Uygulamayı çalıştırın ve DB'nin tohumlu olduğunu doğrulayın.

### <a name="inspect-the-database"></a>Veritabanını inceleyin

DB'yi incelemek için **SQL Server Object Explorer'ı** kullanın. Bir `__EFMigrationsHistory` tablo eklenmesine dikkat edin. Tablo, DB'ye `__EFMigrationsHistory` hangi geçişlerin uygulandığını izler. Tablodaki verileri `__EFMigrationsHistory` görüntüleyin, ilk geçiş için bir satır gösterir. Önceki CLI çıktısı örneğindeki son günlük, bu satırı oluşturan INSERT deyimini gösterir.

Uygulamayı çalıştırın ve her şeyin çalıştığını doğrulayın.

## <a name="applying-migrations-in-production"></a>Üretimde göçlerin uygulanması

Üretim uygulamalarının uygulama nın başlatılmasında [Database.Migrate'yi](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **aramamalarını** öneririz. `Migrate`sunucu çiftliğindeki bir uygulamadan çağrılmamalıdır. Örneğin, uygulama ölçeklendirme ile bulut dağıtılmışsa (uygulamanın birden çok örneği çalışıyorsa).

Veritabanı geçişi dağıtımın bir parçası olarak ve kontrollü bir şekilde yapılmalıdır. Üretim veritabanı geçiş yaklaşımları şunlardır:

* SQL komut dosyaları oluşturmak için geçişleri kullanma ve dağıtımda SQL komut dosyalarını kullanma.
* Kontrollü `dotnet ef database update` bir ortamdan kaçıyor.

EF Core, `__MigrationsHistory` geçişlerin çalışması gerekip gerekmeden olmadığını görmek için tabloyu kullanır. DB güncelse, geçiş çalıştırılmayı zedler.

## <a name="troubleshooting"></a>Sorun giderme

Tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)indirin.

Uygulama aşağıdaki özel durumu oluşturur:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Çözüm: Çalıştır`dotnet ef database update`

### <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Çekirdek CLI](/ef/core/miscellaneous/cli/dotnet).
* [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/sort-filter-page)
> [Sonraki](xref:data/ef-rp/complex-data-model)

::: moniker-end

