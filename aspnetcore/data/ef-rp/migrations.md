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
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="2a38b-103">ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Göçler - 8'de 4</span><span class="sxs-lookup"><span data-stu-id="2a38b-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="2a38b-104">Yazar: [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), ve Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2a38b-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a38b-105">Bu öğretici, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini tanıtır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="2a38b-106">Yeni bir uygulama geliştirildiğinde, veri modeli sık sık değişir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="2a38b-107">Model her değiştiğinde, model veritabanıyla eşitlenir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="2a38b-108">Bu öğretici seri, Varlık Çerçevesi'ni yoksa veritabanını oluşturmak üzere yapılandırarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="2a38b-109">Veri modeli her değiştiğinde, veritabanını bırakmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="2a38b-110">Uygulama bir sonraki çalıştığında, veritabanını yeni veri modeliyle eşleşecek şekilde `EnsureCreated` yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="2a38b-111">Sınıf `DbInitializer` daha sonra yeni veritabanı tohum çalışır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="2a38b-112">Veritabanını veri modeliyle eşit tutma yaklaşımı, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="2a38b-113">Uygulama üretimde çalışırken, genellikle korunması gereken verileri depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="2a38b-114">Uygulama, her değişiklik yapıldığında (yeni bir sütun ekleme gibi) bir test veritabanıyla başalamaz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="2a38b-115">EF Core Geçişler özelliği, EF Core'un yeni bir veritabanı oluşturmak yerine veritabanı şemasını güncelleştirmesini sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="2a38b-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="2a38b-116">Veri modeli değiştiğinde veritabanını bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve varolan verileri korur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="2a38b-117">Veritabanını bırak</span><span class="sxs-lookup"><span data-stu-id="2a38b-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a38b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a38b-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a38b-119">Veritabanını silmek veya Paket Yöneticisi Konsolu'nda (PMC) **Package Manager Console** aşağıdaki komutu çalıştırmak için SQL Server **Object Explorer'ı** (SSOX) kullanın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a38b-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a38b-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a38b-121">EF CLI'yi yüklemek için aşağıdaki komut u komutisteminde çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="2a38b-122">Komut isteminde, proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="2a38b-123">Proje klasörü *ContosoUniversity.csproj* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="2a38b-124">*CU.db* dosyasını silin veya aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="2a38b-125">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="2a38b-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a38b-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a38b-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a38b-127">PMC'de aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a38b-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a38b-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a38b-129">Komut isteminin proje klasöründe olduğundan emin olun ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="2a38b-130">Yukarı ve Aşağı yöntemleri</span><span class="sxs-lookup"><span data-stu-id="2a38b-130">Up and Down methods</span></span>

<span data-ttu-id="2a38b-131">EF Core `migrations add` komutu veritabanını oluşturmak için kod oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="2a38b-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="2a38b-132">Bu geçişler kodu *Geçişler\<zaman damgası>_InitialCreate.cs* dosyasında dır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2a38b-133">`InitialCreate` Sınıfın `Up` yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="2a38b-134">Yöntem, `Down` aşağıdaki örnekte gösterildiği gibi, bunları siler:</span><span class="sxs-lookup"><span data-stu-id="2a38b-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="2a38b-135">Önceki kod ilk geçiş içindir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="2a38b-136">Kod:</span><span class="sxs-lookup"><span data-stu-id="2a38b-136">The code:</span></span>

* <span data-ttu-id="2a38b-137">komutu `migrations add InitialCreate` tarafından oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="2a38b-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="2a38b-138">Komut tarafından `database update` yürütülür.</span><span class="sxs-lookup"><span data-stu-id="2a38b-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="2a38b-139">Veritabanı bağlam sınıfı tarafından belirtilen veri modeli için bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="2a38b-140">Dosya adı için geçiş adı parametresi (örnekte "InitialCreate" ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="2a38b-141">Geçiş adı geçerli bir dosya adı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="2a38b-142">Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="2a38b-143">Örneğin, bir bölüm tablosu eklenen bir geçiş "AddDepartmentTable" olarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="2a38b-144">Geçişler geçmişi tablosu</span><span class="sxs-lookup"><span data-stu-id="2a38b-144">The migrations history table</span></span>

* <span data-ttu-id="2a38b-145">Veritabanını incelemek için SSOX'ı veya SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="2a38b-146">Bir `__EFMigrationsHistory` tablo eklenmesine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2a38b-147">Tablo, `__EFMigrationsHistory` veritabanına hangi geçişlerin uygulandığını izler.</span><span class="sxs-lookup"><span data-stu-id="2a38b-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="2a38b-148">Tablodaki verileri `__EFMigrationsHistory` görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2a38b-149">İlk geçiş için bir satır gösterir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="2a38b-150">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="2a38b-150">The data model snapshot</span></span>

<span data-ttu-id="2a38b-151">*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veri modelinin *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="2a38b-152">Geçiş eklediğinizde, EF geçerli veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="2a38b-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="2a38b-153">Anlık görüntü dosyası veri modelinin durumunu izlediğinden, dosyayı `<timestamp>_<migrationname>.cs` silerek geçişi silemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="2a38b-154">En son geçişi geri almak için komutu `migrations remove` kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="2a38b-155">Bu komut geçişi siler ve anlık görüntünün doğru şekilde sıfırlanın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="2a38b-156">Daha fazla bilgi için [dotnet ef geçişleri kaldırın](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)bakın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="2a38b-157">EnsureCreated'ı kaldırma</span><span class="sxs-lookup"><span data-stu-id="2a38b-157">Remove EnsureCreated</span></span>

<span data-ttu-id="2a38b-158">Bu öğretici serisi `EnsureCreated`kullanılarak başladı.</span><span class="sxs-lookup"><span data-stu-id="2a38b-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="2a38b-159">`EnsureCreated`geçişler tarih tablosu oluşturmaz ve bu nedenle geçişlerle kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="2a38b-160">Veritabanının sık sık bırakıldığı ve yeniden oluşturulduğu yerde test veya hızlı prototipleme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="2a38b-161">Bu noktadan itibaren, öğreticiler geçişleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="2a38b-162">*Data/DBInitializer.cs*olarak, aşağıdaki satırı dışarı yorum:</span><span class="sxs-lookup"><span data-stu-id="2a38b-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="2a38b-163">Uygulamayı çalıştırın ve veritabanının tohumlu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="2a38b-164">Üretimde göçlerin uygulanması</span><span class="sxs-lookup"><span data-stu-id="2a38b-164">Applying migrations in production</span></span>

<span data-ttu-id="2a38b-165">Üretim uygulamalarının uygulama nın başlatılmasında [Database.Migrate'i](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **aramamalarını** öneririz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="2a38b-166">`Migrate`bir sunucu çiftliğine dağıtılan bir uygulamadan çağrılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="2a38b-167">Uygulama birden çok sunucu örneğine ölçeklendirildiyse, veritabanı şema güncelleştirmelerinin birden çok sunucudan gerçekleşmemesini veya okuma/yazma erişimiyle çakışmamasını sağlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="2a38b-168">Veritabanı geçişi dağıtımın bir parçası olarak ve kontrollü bir şekilde yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="2a38b-169">Üretim veritabanı geçiş yaklaşımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2a38b-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="2a38b-170">SQL komut dosyaları oluşturmak için geçişleri kullanma ve dağıtımda SQL komut dosyalarını kullanma.</span><span class="sxs-lookup"><span data-stu-id="2a38b-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="2a38b-171">Kontrollü `dotnet ef database update` bir ortamdan kaçıyor.</span><span class="sxs-lookup"><span data-stu-id="2a38b-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2a38b-172">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="2a38b-172">Troubleshooting</span></span>

<span data-ttu-id="2a38b-173">Uygulama SQL Server LocalDB kullanıyorsa ve aşağıdaki özel durumu görüntülerse:</span><span class="sxs-lookup"><span data-stu-id="2a38b-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="2a38b-174">Çözüm bir komut `dotnet ef database update` istemi çalıştırmak için olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2a38b-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2a38b-175">Additional resources</span></span>

* <span data-ttu-id="2a38b-176">[EF Çekirdek CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="2a38b-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="2a38b-177">Paket Yöneticisi Konsolu (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2a38b-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="2a38b-178">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="2a38b-178">Next steps</span></span>

<span data-ttu-id="2a38b-179">Sonraki öğretici, varlık özellikleri ve yeni varlıklar ekleyerek veri modelini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2a38b-180">[Önceki öğretici](xref:data/ef-rp/sort-filter-page)
> [Sonraki öğretici](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="2a38b-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a38b-181">Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="2a38b-182">Çözemediğiniz sorunlarla karşılaştıysanız, tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="2a38b-183">Yeni bir uygulama geliştirildiğinde, veri modeli sık sık değişir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="2a38b-184">Model her değiştiğinde, model veritabanıyla eşitlenir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="2a38b-185">Bu öğretici, varlık çerçevesi varsa veritabanı oluşturmak için yapılandırılarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="2a38b-186">Veri modeli her değiştiğinde:</span><span class="sxs-lookup"><span data-stu-id="2a38b-186">Each time the data model changes:</span></span>

* <span data-ttu-id="2a38b-187">DB düştü.</span><span class="sxs-lookup"><span data-stu-id="2a38b-187">The DB is dropped.</span></span>
* <span data-ttu-id="2a38b-188">EF, modelle eşleşen yeni bir model oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="2a38b-189">Uygulama, DB'yi test verileriyle tohumlar.</span><span class="sxs-lookup"><span data-stu-id="2a38b-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="2a38b-190">DB'yi veri modeliyle eşit tutma yaklaşımı, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="2a38b-191">Uygulama üretimde çalışırken, genellikle korunması gereken verileri depolamaktır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="2a38b-192">Uygulama, her değişiklik yapıldığında (yeni bir sütun ekleme gibi) bir test DB ile başalamaz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="2a38b-193">EF Core Migrations özelliği, EF Core'un yeni bir DB oluşturmak yerine DB şemasını güncelleştirmesini sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="2a38b-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="2a38b-194">Veri modeli değiştiğinde DB'yi bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve varolan verileri korur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="2a38b-195">Veritabanını bırak</span><span class="sxs-lookup"><span data-stu-id="2a38b-195">Drop the database</span></span>

<span data-ttu-id="2a38b-196">**SQL Server Object Explorer** (SSOX) veya komutu `database drop` kullanın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a38b-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a38b-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a38b-198">Paket **Yöneticisi Konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="2a38b-199">Yardım `Get-Help about_EntityFrameworkCore` bilgileri almak için PMC'den çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a38b-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a38b-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a38b-201">Komut penceresini açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="2a38b-202">Proje klasörü *Startup.cs* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="2a38b-203">Komut penceresine aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="2a38b-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="2a38b-204">İlk geçiş oluşturma ve DB'yi güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="2a38b-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="2a38b-205">Projeyi oluşturun ve ilk geçişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2a38b-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a38b-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a38b-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a38b-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a38b-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="2a38b-208">Yukarı ve Aşağı yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="2a38b-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="2a38b-209">EF Core `migrations add` komutu DB oluşturmak için kod oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="2a38b-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="2a38b-210">Bu geçişler kodu *Geçişler\<zaman damgası>_InitialCreate.cs* dosyasında dır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2a38b-211">`InitialCreate` Sınıfın `Up` yöntemi, veri modeli varlık kümelerine karşılık gelen DB tablolarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="2a38b-212">Yöntem, `Down` aşağıdaki örnekte gösterildiği gibi, bunları siler:</span><span class="sxs-lookup"><span data-stu-id="2a38b-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="2a38b-213">Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak için yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="2a38b-214">Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="2a38b-215">Önceki kod ilk geçiş içindir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="2a38b-216">Bu kod `migrations add InitialCreate` komut çalıştırıldığında oluşturuldu.</span><span class="sxs-lookup"><span data-stu-id="2a38b-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="2a38b-217">Dosya adı için geçiş adı parametresi (örnekte "InitialCreate" ) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="2a38b-218">Geçiş adı geçerli bir dosya adı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="2a38b-219">Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="2a38b-220">Örneğin, bir bölüm tablosu eklenen bir geçiş "AddDepartmentTable" olarak adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="2a38b-221">İlk geçiş oluşturulur ve DB varsa:</span><span class="sxs-lookup"><span data-stu-id="2a38b-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="2a38b-222">DB oluşturma kodu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="2a38b-223">DB zaten veri modeliyle eşleştiğinden, DB oluşturma kodunun çalışmasına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="2a38b-224">DB oluşturma kodu çalıştırılırsa, DB zaten veri modeliyle eşleştiğinden herhangi bir değişiklik yapmaz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="2a38b-225">Uygulama yeni bir ortama dağıtıldığında, DB oluşturma kodunun DB'yi oluşturmak için çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="2a38b-226">Daha önce DB düştü ve yok, bu nedenle geçişler yeni DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="2a38b-227">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="2a38b-227">The data model snapshot</span></span>

<span data-ttu-id="2a38b-228">*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="2a38b-229">Geçiş eklediğinizde, EF veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="2a38b-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="2a38b-230">Geçişi silmek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="2a38b-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a38b-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a38b-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a38b-232">Kaldırma-Geçiş</span><span class="sxs-lookup"><span data-stu-id="2a38b-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a38b-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a38b-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="2a38b-234">Daha fazla bilgi için [dotnet ef geçişleri kaldırın](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)bakın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="2a38b-235">Geçişleri kaldır komutu geçişi siler ve anlık görüntünün doğru şekilde sıfırlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2a38b-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="2a38b-236">EnsureCreated'ı kaldırın ve uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="2a38b-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="2a38b-237">Erken gelişim `EnsureCreated` için, kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="2a38b-238">Bu öğreticide, geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="2a38b-239">`EnsureCreated`aşağıdaki sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="2a38b-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="2a38b-240">Geçişleri atlar ve DB ve şema oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2a38b-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="2a38b-241">Geçiş tablosu oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="2a38b-242">Geçişlerde *kullanılamaz.*</span><span class="sxs-lookup"><span data-stu-id="2a38b-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="2a38b-243">DB'nin sık sık bırakıldığı ve yeniden oluşturulduğu test veya hızlı prototipleme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="2a38b-244">Kaldırın: `EnsureCreated`</span><span class="sxs-lookup"><span data-stu-id="2a38b-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="2a38b-245">Uygulamayı çalıştırın ve DB'nin tohumlu olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="2a38b-246">Veritabanını inceleyin</span><span class="sxs-lookup"><span data-stu-id="2a38b-246">Inspect the database</span></span>

<span data-ttu-id="2a38b-247">DB'yi incelemek için **SQL Server Object Explorer'ı** kullanın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="2a38b-248">Bir `__EFMigrationsHistory` tablo eklenmesine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="2a38b-249">Tablo, DB'ye `__EFMigrationsHistory` hangi geçişlerin uygulandığını izler.</span><span class="sxs-lookup"><span data-stu-id="2a38b-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="2a38b-250">Tablodaki verileri `__EFMigrationsHistory` görüntüleyin, ilk geçiş için bir satır gösterir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="2a38b-251">Önceki CLI çıktısı örneğindeki son günlük, bu satırı oluşturan INSERT deyimini gösterir.</span><span class="sxs-lookup"><span data-stu-id="2a38b-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="2a38b-252">Uygulamayı çalıştırın ve her şeyin çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2a38b-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="2a38b-253">Üretimde göçlerin uygulanması</span><span class="sxs-lookup"><span data-stu-id="2a38b-253">Applying migrations in production</span></span>

<span data-ttu-id="2a38b-254">Üretim uygulamalarının uygulama nın başlatılmasında [Database.Migrate'yi](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **aramamalarını** öneririz.</span><span class="sxs-lookup"><span data-stu-id="2a38b-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="2a38b-255">`Migrate`sunucu çiftliğindeki bir uygulamadan çağrılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="2a38b-256">Örneğin, uygulama ölçeklendirme ile bulut dağıtılmışsa (uygulamanın birden çok örneği çalışıyorsa).</span><span class="sxs-lookup"><span data-stu-id="2a38b-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="2a38b-257">Veritabanı geçişi dağıtımın bir parçası olarak ve kontrollü bir şekilde yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="2a38b-258">Üretim veritabanı geçiş yaklaşımları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="2a38b-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="2a38b-259">SQL komut dosyaları oluşturmak için geçişleri kullanma ve dağıtımda SQL komut dosyalarını kullanma.</span><span class="sxs-lookup"><span data-stu-id="2a38b-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="2a38b-260">Kontrollü `dotnet ef database update` bir ortamdan kaçıyor.</span><span class="sxs-lookup"><span data-stu-id="2a38b-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="2a38b-261">EF Core, `__MigrationsHistory` geçişlerin çalışması gerekip gerekmeden olmadığını görmek için tabloyu kullanır.</span><span class="sxs-lookup"><span data-stu-id="2a38b-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="2a38b-262">DB güncelse, geçiş çalıştırılmayı zedler.</span><span class="sxs-lookup"><span data-stu-id="2a38b-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2a38b-263">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="2a38b-263">Troubleshooting</span></span>

<span data-ttu-id="2a38b-264">Tamamlanan [uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)indirin.</span><span class="sxs-lookup"><span data-stu-id="2a38b-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="2a38b-265">Uygulama aşağıdaki özel durumu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2a38b-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="2a38b-266">Çözüm: Çalıştır`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="2a38b-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2a38b-267">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2a38b-267">Additional resources</span></span>

* [<span data-ttu-id="2a38b-268">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="2a38b-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="2a38b-269">[.NET Çekirdek CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="2a38b-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="2a38b-270">Paket Yöneticisi Konsolu (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="2a38b-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="2a38b-271">[Önceki](xref:data/ef-rp/sort-filter-page)
> [Sonraki](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="2a38b-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

