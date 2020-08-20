---
title: Bölüm 4, Razor ASP.NET Core geçişlerde EF Core olan sayfalar
author: rick-anderson
description: RazorSayfaların 4. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/migrations
ms.openlocfilehash: d922e3a4ad3660bdd1c70dc262acc2f87bdd4214
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627006"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Bölüm 4, Razor ASP.NET Core EF Core geçişleri olan sayfalar

, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği tanıtılmıştır.

Yeni bir uygulama geliştirildiğinde, veri modeli sıklıkla değişir. Modelin her değiştirilişinde, model veritabanıyla eşitlenmemiş olur. Bu öğretici serisi, mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırılarak başlatılır. Veri modelinin her değiştirilişinde veritabanını bırakmalısınız. Uygulamanın bir sonraki çalıştırılışında, `EnsureCreated` Yeni veri modeliyle eşleşecek şekilde veritabanını yeniden oluşturur. `DbInitializer`Daha sonra sınıfı yeni veritabanını temel alarak çalışır.

Veritabanını veri modeliyle eşitlenmiş halde tutmaya yönelik bu yaklaşım, uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalıştığında genellikle saklanması gereken verileri depolar. Uygulama her değişiklik yapıldığında (yeni sütun ekleme gibi) bir test veritabanıyla başlayamaz. EF Core geçişleri özelliği, yeni bir veritabanı oluşturmak yerine EF Core veritabanı şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.

Veri modeli değiştiğinde veritabanını bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve var olan verileri korur.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Veritabanını bırak

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanını silmek için **SQL Server Nesne Gezgini** (ssox) kullanın veya **Paket Yöneticisi konsolunda** (PMC) şu komutu çalıştırın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* EF CLı 'yi yüklemek için komut isteminde aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* Komut isteminde proje klasörüne gidin. Proje klasörü *Contosouniversity. csproj* dosyasını içerir.

* *Cu. db* dosyasını silin veya şu komutu çalıştırın:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>İlk geçiş oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

PMC 'de şu komutları çalıştırın:

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

## <a name="up-and-down-methods"></a>Yukarı ve aşağı Yöntemler

EF Core `migrations add` komutu veritabanını oluşturmak için kod oluşturdu. Bu geçiş kodu *geçişlerde \<timestamp> _InitialCreate. cs* dosyasında bulunur. `Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur. `Down`Yöntemi, aşağıdaki örnekte gösterildiği gibi bunları siler:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Önceki kod ilk geçişe yöneliktir. Kod:

* Komut tarafından oluşturuldu `migrations add InitialCreate` . 
* Komutu tarafından yürütülür `database update` .
* Veritabanı bağlamı sınıfı tarafından belirtilen veri modeli için bir veritabanı oluşturur.

Dosya adı için geçiş adı parametresi (örnekteki "ınitialcreate") kullanılır. Geçiş adı herhangi bir geçerli dosya adı olabilir. Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, bir departman tablosu ekleyen bir geçişe "AddDepartmentTable" adı verilir.

## <a name="the-migrations-history-table"></a>Geçişler geçmiş tablosu

* Veritabanını incelemek için SSOX veya SQLite aracınızı kullanın.
* Tablo ekleme hakkında dikkat edin `__EFMigrationsHistory` . `__EFMigrationsHistory`Tablo, hangi geçişlerin veritabanına uygulandığını izler.
* Tablodaki verileri görüntüleyin `__EFMigrationsHistory` . İlk geçiş için bir satır gösterir.

## <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

Geçişler, *geçiş/SchoolContextModelSnapshot. cs*içindeki geçerli veri modelinin *anlık görüntüsünü* oluşturur. Bir geçiş eklediğinizde, EF geçerli veri modelini Snapshot dosyası ile karşılaştırarak nelerin değiştirildiğini belirler.

Anlık görüntü dosyası veri modelinin durumunu izlediğinden, dosyayı silerek bir geçişi silemezsiniz `<timestamp>_<migrationname>.cs` . En son geçişi geri yüklemek için komutunu kullanmanız gerekir `migrations remove` . Bu komut, geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar. Daha fazla bilgi için bkz. [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Yeniden oluşturulmasını kaldır

Bu öğretici serisi kullanılarak başlatıldı `EnsureCreated` . `EnsureCreated` geçişler geçmişi tablosu oluşturmaz ve geçişler ile kullanılamaz. Bu, veritabanının düşürülme ve sıklıkla yeniden oluşturulduğu test veya hızlı prototip oluşturma için tasarlanmıştır.

Bu noktadan sonra öğreticiler, geçişleri kullanacaktır.

*Data/Dbınınitializer. cs*dosyasında aşağıdaki satırı açıklama olarak inceleyin:

```csharp
context.Database.EnsureCreated();
```
Uygulamayı çalıştırın ve veritabanının çalıştığını doğrulayın.

## <a name="applying-migrations-in-production"></a>Üretimde geçişleri uygulama

Uygulama başlangıcında, üretim uygulamalarının [Database. Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **olarak çağırmalarını** öneririz. `Migrate` sunucu grubuna dağıtılan bir uygulamadan çağrılmamalıdır. Uygulama birden çok sunucu örneğine ölçekleniyorsa, veritabanı şeması güncelleştirmelerinin birden çok sunucudan oluşmaması veya okuma/yazma erişimiyle çakışmamasını sağlamak zordur.

Veritabanı geçişi, dağıtımın bir parçası olarak ve denetimli bir şekilde yapılmalıdır. Üretim veritabanı geçiş yaklaşımları şunları içerir:

* SQL betikleri oluşturmak ve dağıtımda SQL betikleri kullanmak için geçişleri kullanma.
* `dotnet ef database update`Denetlenen bir ortamdan çalıştırma.

## <a name="troubleshooting"></a>Sorun giderme

Uygulama SQL Server LocalDB kullanıyorsa ve aşağıdaki özel durumu görüntülüyorsa:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Çözüm, `dotnet ef database update` bir komut isteminde çalıştırılabilir.

### <a name="additional-resources"></a>Ek kaynaklar

* [Clı EF Core](/ef/core/miscellaneous/cli/dotnet).
* [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide, veri modeli, varlık özellikleri ve yeni varlıklar eklenerek oluşturulur.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/sort-filter-page) 
>  [Sonraki öğretici](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği kullanılır.

Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.

Yeni bir uygulama geliştirildiğinde, veri modeli sıklıkla değişir. Modelin her değiştirilişinde, model veritabanıyla eşitlenmemiş olur. Bu öğretici, mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırılarak başlatılır. Veri modelinin her değiştirilişinde:

* DB bırakılır.
* EF, modelle eşleşen yeni bir tane oluşturur.
* Uygulama, DB 'yi test verileriyle birlikte oluşturur.

Bu yaklaşım, VERITABANıNı veri modeliyle eşitlenmiş halde tutmak, uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalıştığında genellikle saklanması gereken verileri depolar. Uygulama her değişiklik yapıldığında (yeni sütun ekleme gibi) bir test DB ile başlayamaz. EF Core geçişleri özelliği, yeni bir VERITABANı oluşturmak yerine EF Core DB şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.

Veri modeli değiştiğinde VERITABANıNı bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve mevcut verileri korur.

## <a name="drop-the-database"></a>Veritabanını bırak

**SQL Server Nesne Gezgini** (ssox) veya komutunu kullanın `database drop` :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

`Get-Help about_EntityFrameworkCore`Yardım bilgileri almak IÇIN PMC 'den çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir komut penceresi açın ve proje klasörüne gidin. Proje klasörü *Startup.cs* dosyasını içerir.

Komut penceresine şunu girin:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>İlk geçiş oluşturma ve VERITABANıNı güncelleştirme

Projeyi derleyin ve ilk geçişi oluşturun.

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

### <a name="examine-the-up-and-down-methods"></a>Yukarı ve aşağı yöntemlerini inceleyin

EF Core `migrations add` komutu veritabanını oluşturmak için kodu oluşturdu. Bu geçiş kodu *geçişlerde \<timestamp> _InitialCreate. cs* dosyasında bulunur. `Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine KARŞıLıK gelen DB tablolarını oluşturur. `Down`Yöntemi, aşağıdaki örnekte gösterildiği gibi bunları siler:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak üzere yöntemini çağırır. Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.

Önceki kod ilk geçişe yöneliktir. Bu kod, `migrations add InitialCreate` komut çalıştırıldığında oluşturulmuştur. Dosya adı için geçiş adı parametresi (örnekteki "ınitialcreate") kullanılır. Geçiş adı herhangi bir geçerli dosya adı olabilir. Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, bir departman tablosu ekleyen bir geçişe "AddDepartmentTable" adı verilir.

İlk geçiş oluşturulur ve VERITABANı varsa:

* DB oluşturma kodu oluşturulur.
* DB, veri modeliyle zaten eşleştiğinden, DB oluşturma kodunun çalıştırılması gerekmiyor. DB oluşturma kodu çalıştırılsa, VERITABANı veri modeliyle zaten eşleştiğinden hiçbir değişiklik yapmaz.

Uygulama yeni bir ortama dağıtıldığında, DB oluşturmak için DB oluşturma kodunun çalıştırılması gerekir.

Daha önce VERITABANı bırakılmıştı ve mevcut olmadığından geçişler yeni DB 'yi oluşturur.

### <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

Geçişler *geçişlerde/SchoolContextModelSnapshot. cs*' de geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur. Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.

Bir geçişi silmek için aşağıdaki komutu kullanın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Geçişi Kaldır

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Daha fazla bilgi için bkz. [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Geçişleri Kaldır komutu geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar.

### <a name="remove-ensurecreated-and-test-the-app"></a>Uygulamayı kaldırın ve uygulamayı test edin

Erken geliştirme için `EnsureCreated` kullanıldı. Bu öğreticide geçişler kullanılır. `EnsureCreated` aşağıdaki sınırlamalara sahiptir:

* Geçişleri atlar ve DB ve şema oluşturur.
* Geçişler tablosu oluşturmaz.
* Geçişlerle *kullanılamaz.*
* , DB 'nin bıraktığı ve sıklıkla yeniden oluşturulduğu test veya hızlı prototipleme için tasarlanmıştır.

Kaldır `EnsureCreated` :

```csharp
context.Database.EnsureCreated();
```

Uygulamayı çalıştırın ve DB 'nin çalıştığını doğrulayın.

### <a name="inspect-the-database"></a>Veritabanını inceleyin

DB 'yi denetlemek için **SQL Server Nesne Gezgini** kullanın. Tablo ekleme hakkında dikkat edin `__EFMigrationsHistory` . `__EFMigrationsHistory`Tablo, hangi GEÇIŞLERIN veritabanına uygulandığını izler. Tablodaki verileri görüntüleme `__EFMigrationsHistory` , ilk geçiş için bir satır gösterir. Önceki CLı çıkış örneğinde yer alan son oturum, bu satırı oluşturan INSERT ifadesini gösterir.

Uygulamayı çalıştırın ve her şeyin çalıştığını doğrulayın.

## <a name="applying-migrations-in-production"></a>Üretimde geçişleri uygulama

Uygulama başlangıcında, üretim uygulamalarının [Database. Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **çağrısını yapmanızı** öneririz. `Migrate` sunucu grubundaki bir uygulamadan çağrılmamalıdır. Örneğin, uygulama bulutu genişleme ile dağıtılmışsa (uygulamanın birden çok örneği çalışır).

Veritabanı geçişi, dağıtımın bir parçası olarak ve denetimli bir şekilde yapılmalıdır. Üretim veritabanı geçiş yaklaşımları şunları içerir:

* SQL betikleri oluşturmak ve dağıtımda SQL betikleri kullanmak için geçişleri kullanma.
* `dotnet ef database update`Denetlenen bir ortamdan çalıştırma.

EF Core, `__MigrationsHistory` herhangi bir geçişin çalıştırılması gerektiğini görmek için tabloyu kullanır. DB güncel değilse, hiçbir geçiş çalıştırılmaz.

## <a name="troubleshooting"></a>Sorun giderme

[Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)indirin.

Uygulama aşağıdaki özel durumu oluşturur:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Çözüm: Çalıştır `dotnet ef database update`

### <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).
* [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/sort-filter-page) 
>  [Sonraki](xref:data/ef-rp/complex-data-model)

::: moniker-end

