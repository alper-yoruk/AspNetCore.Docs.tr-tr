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
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="fbf36-103">Bölüm 7, ASP.NET Core bir sayfaya yeni bir alan ekleyin Razor</span><span class="sxs-lookup"><span data-stu-id="fbf36-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="fbf36-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fbf36-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fbf36-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fbf36-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fbf36-106">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fbf36-107">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-107">Add a new field to the model.</span></span>
* <span data-ttu-id="fbf36-108">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fbf36-109">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fbf36-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fbf36-110">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fbf36-111">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fbf36-112">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fbf36-113">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fbf36-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="fbf36-114">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="fbf36-115">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-115">Build the app.</span></span>

1. <span data-ttu-id="fbf36-116">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="fbf36-117">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-117">Update the following pages:</span></span>
   1. <span data-ttu-id="fbf36-118">`Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="fbf36-119">[Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="fbf36-120">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fbf36-121">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fbf36-122">Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fbf36-123">`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fbf36-124">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fbf36-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fbf36-125">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fbf36-126">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fbf36-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fbf36-127">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fbf36-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fbf36-128">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fbf36-129">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fbf36-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="fbf36-130">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fbf36-131">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fbf36-132">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fbf36-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fbf36-133">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fbf36-134">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fbf36-135">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fbf36-136">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fbf36-137">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fbf36-138">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fbf36-139">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fbf36-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fbf36-141">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fbf36-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="fbf36-142">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="fbf36-143">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="fbf36-144">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fbf36-145">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fbf36-146">Veritabanı şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fbf36-147">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fbf36-148">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fbf36-149">`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fbf36-150">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fbf36-151">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fbf36-152">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fbf36-153">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fbf36-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="fbf36-154">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="fbf36-155">Veritabanına sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="fbf36-156">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="fbf36-157">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-157">Select **OK**.</span></span>
1. <span data-ttu-id="fbf36-158">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fbf36-159">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fbf36-160">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fbf36-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fbf36-161">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="fbf36-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fbf36-162">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fbf36-163">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fbf36-164">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fbf36-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fbf36-165">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fbf36-166">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fbf36-167">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fbf36-168">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fbf36-169">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fbf36-170">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-170">Creating a new table.</span></span>
>* <span data-ttu-id="fbf36-171">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fbf36-172">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-172">Dropping the old table.</span></span>
>* <span data-ttu-id="fbf36-173">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-173">Renaming the new table.</span></span>
>
><span data-ttu-id="fbf36-174">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fbf36-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fbf36-175">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fbf36-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fbf36-176">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbf36-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fbf36-177">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fbf36-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fbf36-178">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fbf36-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="fbf36-179">Geçiş klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="fbf36-180">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="fbf36-181">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fbf36-182">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbf36-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fbf36-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fbf36-184">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fbf36-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="fbf36-185">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fbf36-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fbf36-186">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fbf36-187">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-187">Add a new field to the model.</span></span>
* <span data-ttu-id="fbf36-188">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fbf36-189">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fbf36-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fbf36-190">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fbf36-191">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fbf36-192">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fbf36-193">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fbf36-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="fbf36-194">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="fbf36-195">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-195">Build the app.</span></span>

1. <span data-ttu-id="fbf36-196">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="fbf36-197">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-197">Update the following pages:</span></span>
   1. <span data-ttu-id="fbf36-198">`Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="fbf36-199">[Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="fbf36-200">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fbf36-201">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fbf36-202">Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fbf36-203">`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fbf36-204">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fbf36-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fbf36-205">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fbf36-206">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fbf36-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fbf36-207">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fbf36-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fbf36-208">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fbf36-209">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fbf36-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="fbf36-210">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fbf36-211">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fbf36-212">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fbf36-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fbf36-213">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fbf36-214">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fbf36-215">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fbf36-216">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fbf36-217">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fbf36-218">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fbf36-219">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fbf36-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fbf36-221">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fbf36-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="fbf36-222">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="fbf36-223">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="fbf36-224">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fbf36-225">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fbf36-226">Veritabanı şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fbf36-227">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fbf36-228">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fbf36-229">`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fbf36-230">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fbf36-231">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fbf36-232">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fbf36-233">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fbf36-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="fbf36-234">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="fbf36-235">Veritabanına sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="fbf36-236">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="fbf36-237">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-237">Select **OK**.</span></span>
* <span data-ttu-id="fbf36-238">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fbf36-239">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fbf36-240">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fbf36-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fbf36-241">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fbf36-242">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fbf36-243">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fbf36-244">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fbf36-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fbf36-245">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fbf36-246">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fbf36-247">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fbf36-248">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fbf36-249">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fbf36-250">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-250">Creating a new table.</span></span>
>* <span data-ttu-id="fbf36-251">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fbf36-252">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-252">Dropping the old table.</span></span>
>* <span data-ttu-id="fbf36-253">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-253">Renaming the new table.</span></span>
>
><span data-ttu-id="fbf36-254">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fbf36-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fbf36-255">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fbf36-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fbf36-256">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbf36-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fbf36-257">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fbf36-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fbf36-258">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fbf36-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="fbf36-259">Geçiş klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="fbf36-260">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="fbf36-261">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fbf36-262">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbf36-263">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fbf36-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fbf36-264">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fbf36-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fbf36-265">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fbf36-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fbf36-266">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fbf36-267">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-267">Add a new field to the model.</span></span>
* <span data-ttu-id="fbf36-268">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fbf36-269">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fbf36-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fbf36-270">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fbf36-271">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fbf36-272">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fbf36-273">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fbf36-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="fbf36-274">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="fbf36-275">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-275">Build the app.</span></span>

<span data-ttu-id="fbf36-276">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="fbf36-277">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-277">Update the following pages:</span></span>

* <span data-ttu-id="fbf36-278">`Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="fbf36-279">[Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="fbf36-280">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fbf36-281">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fbf36-282">Uygulama Şu anda çalıştırıldığında, uygulama şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fbf36-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fbf36-283">Bu hata, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fbf36-284">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fbf36-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fbf36-285">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fbf36-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fbf36-286">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fbf36-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fbf36-287">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fbf36-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fbf36-288">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fbf36-289">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fbf36-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="fbf36-290">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fbf36-291">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fbf36-292">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fbf36-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fbf36-293">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fbf36-294">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fbf36-295">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fbf36-296">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fbf36-297">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fbf36-298">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fbf36-299">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fbf36-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fbf36-301">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fbf36-301">Add a migration for the rating field</span></span>

<span data-ttu-id="fbf36-302">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="fbf36-303">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="fbf36-304">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fbf36-305">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fbf36-306">Veritabanı şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fbf36-307">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fbf36-308">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fbf36-309">`Update-Database`Komutu, çerçeveye şema değişikliklerini veritabanına uygulamasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fbf36-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fbf36-310">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fbf36-311">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fbf36-312">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fbf36-313">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fbf36-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="fbf36-314">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="fbf36-315">Veritabanına sağ tıklayın ve **Sil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="fbf36-316">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="fbf36-317">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-317">Select **OK**.</span></span>
* <span data-ttu-id="fbf36-318">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fbf36-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fbf36-319">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fbf36-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fbf36-320">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fbf36-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fbf36-321">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="fbf36-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fbf36-322">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fbf36-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fbf36-323">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fbf36-324">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fbf36-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fbf36-325">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fbf36-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fbf36-326">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fbf36-327">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fbf36-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fbf36-328">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fbf36-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fbf36-329">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fbf36-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fbf36-330">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-330">Creating a new table.</span></span>
>* <span data-ttu-id="fbf36-331">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fbf36-332">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-332">Dropping the old table.</span></span>
>* <span data-ttu-id="fbf36-333">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fbf36-333">Renaming the new table.</span></span>
>
><span data-ttu-id="fbf36-334">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fbf36-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fbf36-335">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fbf36-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fbf36-336">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fbf36-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fbf36-337">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fbf36-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fbf36-338">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fbf36-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="fbf36-339">Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fbf36-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fbf36-340">Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin.</span><span class="sxs-lookup"><span data-stu-id="fbf36-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="fbf36-341">Ardından şu `ef database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fbf36-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="fbf36-342">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fbf36-343">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fbf36-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbf36-344">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fbf36-344">Additional resources</span></span>

* [<span data-ttu-id="fbf36-345">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="fbf36-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="fbf36-346">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fbf36-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
