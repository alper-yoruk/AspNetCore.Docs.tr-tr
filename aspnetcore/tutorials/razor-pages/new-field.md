---
title: Bölüm 7, ASP.NET Core bir sayfaya yeni bir alan ekleyin Razor
author: rick-anderson
description: Sayfalardaki eğitim serisinin Bölüm 7 Razor .
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: f5efb121b95d84876c9029cf06a96f827137e660
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631458"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="2e860-103">Bölüm 7, ASP.NET Core bir sayfaya yeni bir alan ekleyin Razor</span><span class="sxs-lookup"><span data-stu-id="2e860-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="2e860-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e860-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="2e860-105">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2e860-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="2e860-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-106">Add a new field to the model.</span></span>
* <span data-ttu-id="2e860-107">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="2e860-108">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="2e860-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="2e860-109">`__EFMigrationsHistory`Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenmiş olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="2e860-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="2e860-110">Model sınıfları DB ile eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2e860-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="2e860-111">Şema/modelin eşitlemede otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="2e860-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="2e860-112">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="2e860-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="2e860-113">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="2e860-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="2e860-114">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2e860-114">Build the app.</span></span>

<span data-ttu-id="2e860-115">*Sayfaları/filmleri/dizini. cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2e860-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="2e860-116">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2e860-116">Update the following pages:</span></span>

* <span data-ttu-id="2e860-117">`Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="2e860-118">[Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="2e860-119">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="2e860-120">VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="2e860-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="2e860-121">Veritabanını güncelleştirmeden uygulamayı çalıştırmak şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="2e860-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="2e860-122">`SqlException`Özel durum, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="2e860-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="2e860-123">( `Rating` Veritabanı tablosunda sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="2e860-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="2e860-124">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="2e860-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="2e860-125">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2e860-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="2e860-126">Bu yaklaşım, geliştirme döngüsünün başlarında daha erken bir yoldur; modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2e860-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="2e860-127">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="2e860-128">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="2e860-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="2e860-129">DB 'yi şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="2e860-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="2e860-130">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="2e860-131">Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e860-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="2e860-132">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="2e860-133">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e860-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="2e860-134">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e860-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="2e860-135">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="2e860-136">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapmak isteyeceksiniz `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="2e860-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="2e860-137">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="2e860-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="2e860-138">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e860-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e860-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="2e860-140">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="2e860-140">Add a migration for the rating field</span></span>

<span data-ttu-id="2e860-141">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="2e860-142">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="2e860-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="2e860-143">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="2e860-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="2e860-144">`Movie`Modeli `Movie` DB şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="2e860-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="2e860-145">DB şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2e860-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="2e860-146">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e860-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="2e860-147">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="2e860-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="2e860-148">`Update-Database`Komutu, çerçeveye şema değişikliklerini uygulamaya uygulayıp mevcut verileri korumasını söyler.</span><span class="sxs-lookup"><span data-stu-id="2e860-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="2e860-149">VERITABANıNDAKI tüm kayıtları silerseniz, başlatıcı DB 'yi temel alır ve `Rating` alanını içerir.</span><span class="sxs-lookup"><span data-stu-id="2e860-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="2e860-150">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="2e860-151">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e860-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="2e860-152">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="2e860-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="2e860-153">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="2e860-154">Veritabanına sağ tıklayın ve *Sil*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="2e860-155">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="2e860-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="2e860-156">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-156">Select **OK**.</span></span>
* <span data-ttu-id="2e860-157">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2e860-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2e860-158">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e860-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="2e860-159">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="2e860-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="2e860-160">Geçiş klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="2e860-160">Delete the migration folder.</span></span>  <span data-ttu-id="2e860-161">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e860-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="2e860-162">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="2e860-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="2e860-163">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="2e860-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e860-164">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e860-164">Additional resources</span></span>

* [<span data-ttu-id="2e860-165">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="2e860-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="2e860-166">[Önceki: arama ekleme](xref:tutorials/razor-pages/search) 
>  [Sonraki: doğrulama ekleme](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="2e860-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="2e860-167">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2e860-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="2e860-168">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-168">Add a new field to the model.</span></span>
* <span data-ttu-id="2e860-169">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="2e860-170">Bir veritabanını otomatik olarak oluşturmak için EF Code First kullanırken Code First:</span><span class="sxs-lookup"><span data-stu-id="2e860-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="2e860-171">Veritabanı şemasının oluşturulduğu model sınıflarıyla uyumlu olup olmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="2e860-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="2e860-172">Model sınıfları DB ile eşitlenmiyorsa, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2e860-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="2e860-173">Şema/modelin eşitlemede otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="2e860-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="2e860-174">Film modeline bir derecelendirme özelliği ekleme</span><span class="sxs-lookup"><span data-stu-id="2e860-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="2e860-175">*Modeller/film. cs* dosyasını açın ve bir özellik ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="2e860-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="2e860-176">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2e860-176">Build the app.</span></span>

<span data-ttu-id="2e860-177">*Sayfaları/filmleri/dizini. cshtml*'yi düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2e860-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="2e860-178">Aşağıdaki sayfaları güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2e860-178">Update the following pages:</span></span>

* <span data-ttu-id="2e860-179">`Rating`Alanı silme ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="2e860-180">[Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) dosyasını bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="2e860-181">`Rating`Alanı düzenleme sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="2e860-182">VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="2e860-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="2e860-183">Şimdi çalıştırırsanız uygulama şunu oluşturur `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="2e860-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="2e860-184">Bu hata, güncelleştirilmiş film modeli sınıfının, veritabanının film tablosunun şemasından farklı olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="2e860-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="2e860-185">( `Rating` Veritabanı tablosunda sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="2e860-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="2e860-186">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="2e860-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="2e860-187">Yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak bırakıp yeniden oluşturmaya Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2e860-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="2e860-188">Bu yaklaşım, geliştirme döngüsünün başlarında daha erken bir yoldur; modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2e860-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="2e860-189">Downsıde, veritabanında var olan verileri kaybetmeniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="2e860-190">Bu yaklaşımı bir üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="2e860-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="2e860-191">DB 'yi şema değişikliklerinde bırakıp bir başlatıcı kullanarak veritabanının test verileriyle otomatik olarak çekirdeğini oluşturmak, genellikle bir uygulama geliştirmeye yönelik üretken bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="2e860-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="2e860-192">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="2e860-193">Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e860-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="2e860-194">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="2e860-195">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e860-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="2e860-196">Bu öğretici için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e860-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="2e860-197">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2e860-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="2e860-198">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her bir blok için bu değişikliği yapmak isteyeceksiniz `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="2e860-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="2e860-199">[Tamamlanan SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="2e860-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="2e860-200">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="2e860-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e860-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e860-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="2e860-202">Derecelendirme alanı için bir geçiş ekleyin</span><span class="sxs-lookup"><span data-stu-id="2e860-202">Add a migration for the rating field</span></span>

<span data-ttu-id="2e860-203">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="2e860-204">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="2e860-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="2e860-205">`Add-Migration`Komut, çerçeveye şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="2e860-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="2e860-206">`Movie`Modeli `Movie` DB şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="2e860-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="2e860-207">DB şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2e860-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="2e860-208">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e860-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="2e860-209">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="2e860-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="2e860-210">`Update-Database`Komutu, çerçeveye şema değişikliklerini veritabanına uygulamasını söyler.</span><span class="sxs-lookup"><span data-stu-id="2e860-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="2e860-211">VERITABANıNDAKI tüm kayıtları silerseniz, başlatıcı DB 'yi temel alır ve `Rating` alanını içerir.</span><span class="sxs-lookup"><span data-stu-id="2e860-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="2e860-212">Bunu, tarayıcıda veya [SQL Server Nesne Gezgini](xref:tutorials/razor-pages/sql#ssox) (ssox) silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e860-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="2e860-213">Başka bir seçenek de veritabanını silmek ve geçişleri kullanarak veritabanını yeniden oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="2e860-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="2e860-214">SSOX 'te veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="2e860-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="2e860-215">SSOX 'te veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="2e860-216">Veritabanına sağ tıklayın ve *Sil*' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="2e860-217">**Mevcut bağlantıları kapat**' a bakın.</span><span class="sxs-lookup"><span data-stu-id="2e860-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="2e860-218">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2e860-218">Select **OK**.</span></span>
* <span data-ttu-id="2e860-219">[PMC](xref:tutorials/razor-pages/new-field#pmc)'de veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2e860-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2e860-220">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e860-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="2e860-221">Veritabanını bırakıp yeniden oluşturun</span><span class="sxs-lookup"><span data-stu-id="2e860-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="2e860-222">Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2e860-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="2e860-223">Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin.</span><span class="sxs-lookup"><span data-stu-id="2e860-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="2e860-224">Ardından şu `ef database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2e860-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="2e860-225">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz/düzenleyebileceğiniz/görüntüleydiğinizi doğrulayın `Rating` .</span><span class="sxs-lookup"><span data-stu-id="2e860-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="2e860-226">Veritabanı birlikte olmazsa, yönteminde bir kesme noktası ayarlayın `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="2e860-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e860-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e860-227">Additional resources</span></span>

* [<span data-ttu-id="2e860-228">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="2e860-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="2e860-229">[Önceki: arama ekleme](xref:tutorials/razor-pages/search) 
>  [Sonraki: doğrulama ekleme](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="2e860-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
