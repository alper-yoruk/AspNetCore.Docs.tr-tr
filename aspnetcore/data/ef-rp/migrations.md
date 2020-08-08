---
title: Bölüm 4, Razor ASP.NET Core geçişlerde EF Core olan sayfalar
author: rick-anderson
description: RazorSayfaların 4. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.date: 07/22/2019
no-loc:
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
ms.openlocfilehash: ebdcf5605e8b4067c52841b138184e51668f9c25
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019657"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="8d6bc-103">Bölüm 4, Razor ASP.NET Core EF Core geçişleri olan sayfalar</span><span class="sxs-lookup"><span data-stu-id="8d6bc-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="8d6bc-104">, [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d6bc-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d6bc-105">Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği tanıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="8d6bc-106">Yeni bir uygulama geliştirildiğinde, veri modeli sıklıkla değişir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="8d6bc-107">Modelin her değiştirilişinde, model veritabanıyla eşitlenmemiş olur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="8d6bc-108">Bu öğretici serisi, mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırılarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="8d6bc-109">Veri modelinin her değiştirilişinde veritabanını bırakmalısınız.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="8d6bc-110">Uygulamanın bir sonraki çalıştırılışında, `EnsureCreated` Yeni veri modeliyle eşleşecek şekilde veritabanını yeniden oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="8d6bc-111">`DbInitializer`Daha sonra sınıfı yeni veritabanını temel alarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="8d6bc-112">Veritabanını veri modeliyle eşitlenmiş halde tutmaya yönelik bu yaklaşım, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="8d6bc-113">Uygulama üretimde çalıştığında genellikle saklanması gereken verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="8d6bc-114">Uygulama her değişiklik yapıldığında (yeni sütun ekleme gibi) bir test veritabanıyla başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="8d6bc-115">EF Core geçişleri özelliği, yeni bir veritabanı oluşturmak yerine EF Core veritabanı şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="8d6bc-116">Veri modeli değiştiğinde veritabanını bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve var olan verileri korur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="8d6bc-117">Veritabanını bırak</span><span class="sxs-lookup"><span data-stu-id="8d6bc-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d6bc-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d6bc-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d6bc-119">Veritabanını silmek için **SQL Server Nesne Gezgini** (ssox) kullanın veya **Paket Yöneticisi konsolunda** (PMC) şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d6bc-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d6bc-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8d6bc-121">EF CLı 'yi yüklemek için komut isteminde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="8d6bc-122">Komut isteminde proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="8d6bc-123">Proje klasörü *Contosouniversity. csproj* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="8d6bc-124">*Cu. db* dosyasını silin veya şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="8d6bc-125">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="8d6bc-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d6bc-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d6bc-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d6bc-127">PMC 'de şu komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d6bc-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d6bc-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d6bc-129">Komut isteminin proje klasöründe olduğundan emin olun ve aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="8d6bc-130">Yukarı ve aşağı Yöntemler</span><span class="sxs-lookup"><span data-stu-id="8d6bc-130">Up and Down methods</span></span>

<span data-ttu-id="8d6bc-131">EF Core `migrations add` komutu veritabanını oluşturmak için kod oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="8d6bc-132">Bu geçiş kodu *geçişlerde \<timestamp> _InitialCreate. cs* dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8d6bc-133">`Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="8d6bc-134">`Down`Yöntemi, aşağıdaki örnekte gösterildiği gibi bunları siler:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="8d6bc-135">Önceki kod ilk geçişe yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="8d6bc-136">Kod:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-136">The code:</span></span>

* <span data-ttu-id="8d6bc-137">Komut tarafından oluşturuldu `migrations add InitialCreate` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="8d6bc-138">Komutu tarafından yürütülür `database update` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="8d6bc-139">Veritabanı bağlamı sınıfı tarafından belirtilen veri modeli için bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="8d6bc-140">Dosya adı için geçiş adı parametresi (örnekteki "ınitialcreate") kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="8d6bc-141">Geçiş adı herhangi bir geçerli dosya adı olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="8d6bc-142">Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="8d6bc-143">Örneğin, bir departman tablosu ekleyen bir geçişe "AddDepartmentTable" adı verilir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="8d6bc-144">Geçişler geçmiş tablosu</span><span class="sxs-lookup"><span data-stu-id="8d6bc-144">The migrations history table</span></span>

* <span data-ttu-id="8d6bc-145">Veritabanını incelemek için SSOX veya SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="8d6bc-146">Tablo ekleme hakkında dikkat edin `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="8d6bc-147">`__EFMigrationsHistory`Tablo, hangi geçişlerin veritabanına uygulandığını izler.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="8d6bc-148">Tablodaki verileri görüntüleyin `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="8d6bc-149">İlk geçiş için bir satır gösterir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="8d6bc-150">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="8d6bc-150">The data model snapshot</span></span>

<span data-ttu-id="8d6bc-151">Geçişler, *geçiş/SchoolContextModelSnapshot. cs*içindeki geçerli veri modelinin *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="8d6bc-152">Bir geçiş eklediğinizde, EF geçerli veri modelini Snapshot dosyası ile karşılaştırarak nelerin değiştirildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="8d6bc-153">Anlık görüntü dosyası veri modelinin durumunu izlediğinden, dosyayı silerek bir geçişi silemezsiniz `<timestamp>_<migrationname>.cs` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="8d6bc-154">En son geçişi geri yüklemek için komutunu kullanmanız gerekir `migrations remove` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="8d6bc-155">Bu komut, geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="8d6bc-156">Daha fazla bilgi için bkz. [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="8d6bc-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="8d6bc-157">Yeniden oluşturulmasını kaldır</span><span class="sxs-lookup"><span data-stu-id="8d6bc-157">Remove EnsureCreated</span></span>

<span data-ttu-id="8d6bc-158">Bu öğretici serisi kullanılarak başlatıldı `EnsureCreated` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="8d6bc-159">`EnsureCreated`geçişler geçmişi tablosu oluşturmaz ve geçişler ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="8d6bc-160">Bu, veritabanının düşürülme ve sıklıkla yeniden oluşturulduğu test veya hızlı prototip oluşturma için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="8d6bc-161">Bu noktadan sonra öğreticiler, geçişleri kullanacaktır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="8d6bc-162">*Data/Dbınınitializer. cs*dosyasında aşağıdaki satırı açıklama olarak inceleyin:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="8d6bc-163">Uygulamayı çalıştırın ve veritabanının çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="8d6bc-164">Üretimde geçişleri uygulama</span><span class="sxs-lookup"><span data-stu-id="8d6bc-164">Applying migrations in production</span></span>

<span data-ttu-id="8d6bc-165">Uygulama başlangıcında, üretim uygulamalarının [Database. Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **olarak çağırmalarını** öneririz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="8d6bc-166">`Migrate`sunucu grubuna dağıtılan bir uygulamadan çağrılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="8d6bc-167">Uygulama birden çok sunucu örneğine ölçekleniyorsa, veritabanı şeması güncelleştirmelerinin birden çok sunucudan oluşmaması veya okuma/yazma erişimiyle çakışmamasını sağlamak zordur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="8d6bc-168">Veritabanı geçişi, dağıtımın bir parçası olarak ve denetimli bir şekilde yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="8d6bc-169">Üretim veritabanı geçiş yaklaşımları şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="8d6bc-170">SQL betikleri oluşturmak ve dağıtımda SQL betikleri kullanmak için geçişleri kullanma.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="8d6bc-171">`dotnet ef database update`Denetlenen bir ortamdan çalıştırma.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8d6bc-172">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="8d6bc-172">Troubleshooting</span></span>

<span data-ttu-id="8d6bc-173">Uygulama SQL Server LocalDB kullanıyorsa ve aşağıdaki özel durumu görüntülüyorsa:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="8d6bc-174">Çözüm, `dotnet ef database update` bir komut isteminde çalıştırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8d6bc-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8d6bc-175">Additional resources</span></span>

* <span data-ttu-id="8d6bc-176">[Clı EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="8d6bc-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="8d6bc-177">Paket Yöneticisi Konsolu (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="8d6bc-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="8d6bc-178">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="8d6bc-178">Next steps</span></span>

<span data-ttu-id="8d6bc-179">Sonraki öğreticide, veri modeli, varlık özellikleri ve yeni varlıklar eklenerek oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8d6bc-180">[Önceki öğretici](xref:data/ef-rp/sort-filter-page) 
>  [Sonraki öğretici](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="8d6bc-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d6bc-181">Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliği kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="8d6bc-182">Çözemediğiniz sorunlarla karşılaşırsanız, [Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)indirin.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="8d6bc-183">Yeni bir uygulama geliştirildiğinde, veri modeli sıklıkla değişir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="8d6bc-184">Modelin her değiştirilişinde, model veritabanıyla eşitlenmemiş olur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="8d6bc-185">Bu öğretici, mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırılarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="8d6bc-186">Veri modelinin her değiştirilişinde:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-186">Each time the data model changes:</span></span>

* <span data-ttu-id="8d6bc-187">DB bırakılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-187">The DB is dropped.</span></span>
* <span data-ttu-id="8d6bc-188">EF, modelle eşleşen yeni bir tane oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="8d6bc-189">Uygulama, DB 'yi test verileriyle birlikte oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="8d6bc-190">Bu yaklaşım, VERITABANıNı veri modeliyle eşitlenmiş halde tutmak, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="8d6bc-191">Uygulama üretimde çalıştığında genellikle saklanması gereken verileri depolar.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="8d6bc-192">Uygulama her değişiklik yapıldığında (yeni sütun ekleme gibi) bir test DB ile başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="8d6bc-193">EF Core geçişleri özelliği, yeni bir VERITABANı oluşturmak yerine EF Core DB şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="8d6bc-194">Veri modeli değiştiğinde VERITABANıNı bırakıp yeniden oluşturmak yerine, geçişler şemayı güncelleştirir ve mevcut verileri korur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="8d6bc-195">Veritabanını bırak</span><span class="sxs-lookup"><span data-stu-id="8d6bc-195">Drop the database</span></span>

<span data-ttu-id="8d6bc-196">**SQL Server Nesne Gezgini** (ssox) veya komutunu kullanın `database drop` :</span><span class="sxs-lookup"><span data-stu-id="8d6bc-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d6bc-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d6bc-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d6bc-198">**Paket Yöneticisi konsolunda** (PMC), aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="8d6bc-199">`Get-Help about_EntityFrameworkCore`Yardım bilgileri almak IÇIN PMC 'den çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d6bc-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d6bc-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d6bc-201">Bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="8d6bc-202">Proje klasörü *Startup.cs* dosyasını içerir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="8d6bc-203">Komut penceresine şunu girin:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="8d6bc-204">İlk geçiş oluşturma ve VERITABANıNı güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="8d6bc-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="8d6bc-205">Projeyi derleyin ve ilk geçişi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d6bc-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d6bc-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d6bc-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d6bc-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="8d6bc-208">Yukarı ve aşağı yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="8d6bc-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="8d6bc-209">EF Core `migrations add` komutu veritabanını oluşturmak için kodu oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="8d6bc-210">Bu geçiş kodu *geçişlerde \<timestamp> _InitialCreate. cs* dosyasında bulunur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8d6bc-211">`Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine KARŞıLıK gelen DB tablolarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="8d6bc-212">`Down`Yöntemi, aşağıdaki örnekte gösterildiği gibi bunları siler:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="8d6bc-213">Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak üzere yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="8d6bc-214">Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="8d6bc-215">Önceki kod ilk geçişe yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="8d6bc-216">Bu kod, `migrations add InitialCreate` komut çalıştırıldığında oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="8d6bc-217">Dosya adı için geçiş adı parametresi (örnekteki "ınitialcreate") kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="8d6bc-218">Geçiş adı herhangi bir geçerli dosya adı olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="8d6bc-219">Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="8d6bc-220">Örneğin, bir departman tablosu ekleyen bir geçişe "AddDepartmentTable" adı verilir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="8d6bc-221">İlk geçiş oluşturulur ve VERITABANı varsa:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="8d6bc-222">DB oluşturma kodu oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="8d6bc-223">DB, veri modeliyle zaten eşleştiğinden, DB oluşturma kodunun çalıştırılması gerekmiyor.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="8d6bc-224">DB oluşturma kodu çalıştırılsa, VERITABANı veri modeliyle zaten eşleştiğinden hiçbir değişiklik yapmaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="8d6bc-225">Uygulama yeni bir ortama dağıtıldığında, DB oluşturmak için DB oluşturma kodunun çalıştırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="8d6bc-226">Daha önce VERITABANı bırakılmıştı ve mevcut olmadığından geçişler yeni DB 'yi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="8d6bc-227">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="8d6bc-227">The data model snapshot</span></span>

<span data-ttu-id="8d6bc-228">Geçişler *geçişlerde/SchoolContextModelSnapshot. cs*' de geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="8d6bc-229">Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="8d6bc-230">Bir geçişi silmek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8d6bc-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d6bc-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8d6bc-232">Geçişi Kaldır</span><span class="sxs-lookup"><span data-stu-id="8d6bc-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8d6bc-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d6bc-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="8d6bc-234">Daha fazla bilgi için bkz. [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="8d6bc-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="8d6bc-235">Geçişleri Kaldır komutu geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="8d6bc-236">Uygulamayı kaldırın ve uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="8d6bc-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="8d6bc-237">Erken geliştirme için `EnsureCreated` kullanıldı.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="8d6bc-238">Bu öğreticide geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="8d6bc-239">`EnsureCreated`aşağıdaki sınırlamalara sahiptir:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="8d6bc-240">Geçişleri atlar ve DB ve şema oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="8d6bc-241">Geçişler tablosu oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="8d6bc-242">Geçişlerle *kullanılamaz.*</span><span class="sxs-lookup"><span data-stu-id="8d6bc-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="8d6bc-243">, DB 'nin bıraktığı ve sıklıkla yeniden oluşturulduğu test veya hızlı prototipleme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="8d6bc-244">Kaldır `EnsureCreated` :</span><span class="sxs-lookup"><span data-stu-id="8d6bc-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="8d6bc-245">Uygulamayı çalıştırın ve DB 'nin çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="8d6bc-246">Veritabanını inceleyin</span><span class="sxs-lookup"><span data-stu-id="8d6bc-246">Inspect the database</span></span>

<span data-ttu-id="8d6bc-247">DB 'yi denetlemek için **SQL Server Nesne Gezgini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="8d6bc-248">Tablo ekleme hakkında dikkat edin `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="8d6bc-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="8d6bc-249">`__EFMigrationsHistory`Tablo, hangi GEÇIŞLERIN veritabanına uygulandığını izler.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="8d6bc-250">Tablodaki verileri görüntüleme `__EFMigrationsHistory` , ilk geçiş için bir satır gösterir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="8d6bc-251">Önceki CLı çıkış örneğinde yer alan son oturum, bu satırı oluşturan INSERT ifadesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="8d6bc-252">Uygulamayı çalıştırın ve her şeyin çalıştığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="8d6bc-253">Üretimde geçişleri uygulama</span><span class="sxs-lookup"><span data-stu-id="8d6bc-253">Applying migrations in production</span></span>

<span data-ttu-id="8d6bc-254">Uygulama başlangıcında, üretim uygulamalarının [Database. Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) **çağrısını yapmanızı** öneririz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="8d6bc-255">`Migrate`sunucu grubundaki bir uygulamadan çağrılmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="8d6bc-256">Örneğin, uygulama bulutu genişleme ile dağıtılmışsa (uygulamanın birden çok örneği çalışır).</span><span class="sxs-lookup"><span data-stu-id="8d6bc-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="8d6bc-257">Veritabanı geçişi, dağıtımın bir parçası olarak ve denetimli bir şekilde yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="8d6bc-258">Üretim veritabanı geçiş yaklaşımları şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="8d6bc-259">SQL betikleri oluşturmak ve dağıtımda SQL betikleri kullanmak için geçişleri kullanma.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="8d6bc-260">`dotnet ef database update`Denetlenen bir ortamdan çalıştırma.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="8d6bc-261">EF Core, `__MigrationsHistory` herhangi bir geçişin çalıştırılması gerektiğini görmek için tabloyu kullanır.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="8d6bc-262">DB güncel değilse, hiçbir geçiş çalıştırılmaz.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8d6bc-263">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="8d6bc-263">Troubleshooting</span></span>

<span data-ttu-id="8d6bc-264">[Tamamlanmış uygulamayı](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)indirin.</span><span class="sxs-lookup"><span data-stu-id="8d6bc-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="8d6bc-265">Uygulama aşağıdaki özel durumu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="8d6bc-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="8d6bc-266">Çözüm: Çalıştır`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="8d6bc-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8d6bc-267">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8d6bc-267">Additional resources</span></span>

* [<span data-ttu-id="8d6bc-268">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="8d6bc-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="8d6bc-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="8d6bc-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="8d6bc-270">Paket Yöneticisi Konsolu (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="8d6bc-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="8d6bc-271">[Önceki](xref:data/ef-rp/sort-filter-page) 
>  [Sonraki](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="8d6bc-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

