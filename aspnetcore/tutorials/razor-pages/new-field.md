---
title: 7. bölüm, yeni bir alan ekleyin
author: rick-anderson
description: Sayfalardaki eğitim serisinin Bölüm 7 Razor .
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: 5263063d82d79dbeeca3e4cec007d240ca8a452a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332186"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="fd1e8-103">Bölüm 7, ASP.NET Core bir sayfaya yeni bir alan ekleyin Razor</span><span class="sxs-lookup"><span data-stu-id="fd1e8-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="fd1e8-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fd1e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fd1e8-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fd1e8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fd1e8-106">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fd1e8-107">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-107">Add a new field to the model.</span></span>
* <span data-ttu-id="fd1e8-108">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fd1e8-109">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fd1e8-110">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fd1e8-111">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fd1e8-112">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fd1e8-113">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="fd1e8-114">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="fd1e8-115">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-115">Build the app.</span></span>

1. <span data-ttu-id="fd1e8-116">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="fd1e8-117">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-117">Update the following pages:</span></span>
   1. <span data-ttu-id="fd1e8-118">`Rating`Alanını Delete ve ayrıntılar sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="fd1e8-119">[ Create . Cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) 'yi bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="fd1e8-120">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fd1e8-121">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fd1e8-122">Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fd1e8-123">`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fd1e8-124">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fd1e8-125">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fd1e8-126">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fd1e8-127">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fd1e8-128">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fd1e8-129">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fd1e8-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="fd1e8-130">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fd1e8-131">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fd1e8-132">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fd1e8-133">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fd1e8-134">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fd1e8-135">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fd1e8-136">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fd1e8-137">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fd1e8-138">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fd1e8-139">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fd1e8-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fd1e8-141">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fd1e8-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="fd1e8-142">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="fd1e8-143">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="fd1e8-144">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fd1e8-145">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fd1e8-146">Create Veritabanı şemasının yeni modele geçirilmesi için kod.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fd1e8-147">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fd1e8-148">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fd1e8-149">`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fd1e8-150">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fd1e8-151">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fd1e8-152">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fd1e8-153">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="fd1e8-154">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="fd1e8-155">Veritabanına sağ tıklayın ve öğesini seçin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="fd1e8-156">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="fd1e8-157">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-157">Select **OK**.</span></span>
1. <span data-ttu-id="fd1e8-158">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fd1e8-159">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fd1e8-160">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fd1e8-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fd1e8-161">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fd1e8-162">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fd1e8-163">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fd1e8-164">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fd1e8-165">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fd1e8-166">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fd1e8-167">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fd1e8-168">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fd1e8-169">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fd1e8-170">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-170">Creating a new table.</span></span>
>* <span data-ttu-id="fd1e8-171">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fd1e8-172">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-172">Dropping the old table.</span></span>
>* <span data-ttu-id="fd1e8-173">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-173">Renaming the new table.</span></span>
>
><span data-ttu-id="fd1e8-174">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fd1e8-175">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fd1e8-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fd1e8-176">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fd1e8-177">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fd1e8-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fd1e8-178">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fd1e8-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="fd1e8-179">Delete Geçiş klasörü.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="fd1e8-180">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="fd1e8-181">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fd1e8-182">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd1e8-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fd1e8-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fd1e8-184">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fd1e8-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="fd1e8-185">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fd1e8-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fd1e8-186">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fd1e8-187">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-187">Add a new field to the model.</span></span>
* <span data-ttu-id="fd1e8-188">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fd1e8-189">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fd1e8-190">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fd1e8-191">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fd1e8-192">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fd1e8-193">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="fd1e8-194">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="fd1e8-195">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-195">Build the app.</span></span>

1. <span data-ttu-id="fd1e8-196">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="fd1e8-197">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-197">Update the following pages:</span></span>
   1. <span data-ttu-id="fd1e8-198">`Rating`Alanını Delete ve ayrıntılar sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="fd1e8-199">[ Create . Cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) 'yi bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="fd1e8-200">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fd1e8-201">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fd1e8-202">Uygulamayı veritabanına güncelleştirme olmadan çalıştırmak şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fd1e8-203">`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fd1e8-204">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fd1e8-205">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fd1e8-206">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fd1e8-207">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fd1e8-208">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fd1e8-209">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fd1e8-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="fd1e8-210">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fd1e8-211">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fd1e8-212">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fd1e8-213">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fd1e8-214">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fd1e8-215">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fd1e8-216">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fd1e8-217">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fd1e8-218">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fd1e8-219">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fd1e8-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fd1e8-221">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fd1e8-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="fd1e8-222">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="fd1e8-223">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="fd1e8-224">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fd1e8-225">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fd1e8-226">Create Veritabanı şemasının yeni modele geçirilmesi için kod.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fd1e8-227">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fd1e8-228">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fd1e8-229">`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fd1e8-230">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fd1e8-231">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fd1e8-232">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fd1e8-233">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="fd1e8-234">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="fd1e8-235">Veritabanına sağ tıklayın ve öğesini seçin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="fd1e8-236">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="fd1e8-237">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-237">Select **OK**.</span></span>
* <span data-ttu-id="fd1e8-238">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fd1e8-239">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fd1e8-240">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fd1e8-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fd1e8-241">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fd1e8-242">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fd1e8-243">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fd1e8-244">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fd1e8-245">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fd1e8-246">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fd1e8-247">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fd1e8-248">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fd1e8-249">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fd1e8-250">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-250">Creating a new table.</span></span>
>* <span data-ttu-id="fd1e8-251">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fd1e8-252">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-252">Dropping the old table.</span></span>
>* <span data-ttu-id="fd1e8-253">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-253">Renaming the new table.</span></span>
>
><span data-ttu-id="fd1e8-254">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fd1e8-255">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fd1e8-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fd1e8-256">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fd1e8-257">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fd1e8-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fd1e8-258">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fd1e8-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="fd1e8-259">Delete Geçiş klasörü.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="fd1e8-260">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="fd1e8-261">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fd1e8-262">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd1e8-263">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fd1e8-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="fd1e8-264">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fd1e8-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd1e8-265">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fd1e8-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fd1e8-266">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="fd1e8-267">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-267">Add a new field to the model.</span></span>
* <span data-ttu-id="fd1e8-268">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="fd1e8-269">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="fd1e8-270">[`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table)Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="fd1e8-271">Model sınıfları veritabanıyla eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="fd1e8-272">Şemanın ve modelin eşitlenmiş olduğunu otomatik doğrulama, tutarsız veritabanı kodu sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="fd1e8-273">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="fd1e8-274">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="fd1e8-275">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-275">Build the app.</span></span>

<span data-ttu-id="fd1e8-276">*Sayfaları/filmleri/ Index . cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="fd1e8-277">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-277">Update the following pages:</span></span>

* <span data-ttu-id="fd1e8-278">`Rating`Alanını Delete ve ayrıntılar sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="fd1e8-279">[ Create . Cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) 'yi bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="fd1e8-280">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="fd1e8-281">Veritabanı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="fd1e8-282">Uygulama Şu anda çalıştırıldığında, uygulama şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="fd1e8-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="fd1e8-283">Bu hata, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="fd1e8-284">`Rating`Veritabanı tablosunda sütun yok.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="fd1e8-285">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="fd1e8-286">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="fd1e8-287">Bu yaklaşım geliştirme döngüsünün başlarında, model ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="fd1e8-288">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="fd1e8-289">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="fd1e8-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="fd1e8-290">Veritabanını şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="fd1e8-291">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="fd1e8-292">Bu yaklaşımın avantajı, verilerin tutulması.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="fd1e8-293">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="fd1e8-294">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="fd1e8-295">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="fd1e8-296">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="fd1e8-297">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapın `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="fd1e8-298">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="fd1e8-299">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fd1e8-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="fd1e8-301">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="fd1e8-301">Add a migration for the rating field</span></span>

<span data-ttu-id="fd1e8-302">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="fd1e8-303">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="fd1e8-304">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="fd1e8-305">`Movie`Modeli `Movie` veritabanı şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="fd1e8-306">Create Veritabanı şemasının yeni modele geçirilmesi için kod.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="fd1e8-307">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="fd1e8-308">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="fd1e8-309">`Update-Database`Komutu, çerçeveye şema değişikliklerini veritabanına uygulamasını söyler.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="fd1e8-310">Veritabanındaki tüm kayıtları silerseniz, başlatıcı veritabanını temel alır ve `Rating` alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="fd1e8-311">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="fd1e8-312">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fd1e8-313">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="fd1e8-314">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="fd1e8-315">Veritabanına sağ tıklayın ve öğesini seçin **Delete** .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="fd1e8-316">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="fd1e8-317">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-317">Select **OK**.</span></span>
* <span data-ttu-id="fd1e8-318">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="fd1e8-319">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd1e8-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="fd1e8-320">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="fd1e8-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="fd1e8-321">Bu öğreticide, mümkün olduğunda Entity Framework Core *geçişleri* özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="fd1e8-322">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="fd1e8-323">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="fd1e8-324">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="fd1e8-325">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="fd1e8-326">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="fd1e8-327">Bunun yerine, şema değiştiğinde veritabanını bırakıp yeniden oluşturursunuz.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="fd1e8-328">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="fd1e8-329">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="fd1e8-330">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-330">Creating a new table.</span></span>
>* <span data-ttu-id="fd1e8-331">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="fd1e8-332">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-332">Dropping the old table.</span></span>
>* <span data-ttu-id="fd1e8-333">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-333">Renaming the new table.</span></span>
>
><span data-ttu-id="fd1e8-334">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="fd1e8-335">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="fd1e8-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="fd1e8-336">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fd1e8-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="fd1e8-337">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="fd1e8-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="fd1e8-338">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="fd1e8-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="fd1e8-339">Delete veritabanını yeniden oluşturmak için veritabanı ve geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="fd1e8-340">Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin.</span><span class="sxs-lookup"><span data-stu-id="fd1e8-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="fd1e8-341">Ardından şu `ef database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fd1e8-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="fd1e8-342">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="fd1e8-343">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="fd1e8-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd1e8-344">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fd1e8-344">Additional resources</span></span>

* [<span data-ttu-id="fd1e8-345">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="fd1e8-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="fd1e8-346">[Önceki: Arama ekle](xref:tutorials/razor-pages/search) 
>  [Sonraki: Doğrulama Ekle](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="fd1e8-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
