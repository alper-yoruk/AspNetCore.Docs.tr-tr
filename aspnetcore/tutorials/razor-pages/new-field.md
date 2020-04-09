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
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="94323-103">ASP.NET Core'daki Razor Page'e yeni bir alan ekleme</span><span class="sxs-lookup"><span data-stu-id="94323-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="94323-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94323-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="94323-105">Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="94323-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="94323-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-106">Add a new field to the model.</span></span>
* <span data-ttu-id="94323-107">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="94323-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="94323-108">Otomatik olarak bir veritabanı oluşturmak için ÖNCE EF Code First'i kullanırken, Önce Kod:</span><span class="sxs-lookup"><span data-stu-id="94323-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="94323-109">Veritabanı `__EFMigrationsHistory` şemasının oluşturulduğu model sınıflarıyla eşitlenip eşitolmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="94323-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="94323-110">Model sınıfları DB ile eşit değilse, EF bir özel durum atar.</span><span class="sxs-lookup"><span data-stu-id="94323-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="94323-111">Şema/modelin eşitlenmiş olarak otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="94323-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="94323-112">Film Modeline Derecelendirme Özelliği Ekleme</span><span class="sxs-lookup"><span data-stu-id="94323-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="94323-113">*Models/Movie.cs* dosyasını açın `Rating` ve bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="94323-114">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="94323-114">Build the app.</span></span>

<span data-ttu-id="94323-115">*Sayfaları/Filmleri/Index.cshtml'i*edin `Rating` ve alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="94323-116">Aşağıdaki sayfaları güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-116">Update the following pages:</span></span>

* <span data-ttu-id="94323-117">`Rating` Alanı Sil ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="94323-118">[Create.cshtml'i](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) `Rating` bir alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="94323-119">`Rating` Alanı Edit Sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="94323-120">DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="94323-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="94323-121">Veritabanını güncellemeden uygulamayı çalıştırmak bir `SqlException`atar:</span><span class="sxs-lookup"><span data-stu-id="94323-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="94323-122">Özel `SqlException` durum, güncelleştirilmiş Film modeli sınıfının veritabanının Film tablosunun şemasından farklı olması nedeniyle kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="94323-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="94323-123">(Veritabanı tablosunda `Rating` sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="94323-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="94323-124">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="94323-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="94323-125">Varlık Çerçevesi'nin yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="94323-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="94323-126">Bu yaklaşım, geliştirme döngüsünün başlarında kullanışlıdır; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="94323-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="94323-127">Dezavantajı veritabanında varolan verileri kaybetmek olduğunu.</span><span class="sxs-lookup"><span data-stu-id="94323-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="94323-128">Bu yaklaşımı üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="94323-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="94323-129">Şema değişikliklerinde DB'yi bırakmak ve veritabanını test verileriyle otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="94323-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="94323-130">Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="94323-131">Bu yaklaşımın avantajı verilerinizi saklamanızdır.</span><span class="sxs-lookup"><span data-stu-id="94323-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="94323-132">Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="94323-133">Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="94323-134">Bu öğretici için Kod İlk Geçişler'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="94323-135">`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="94323-136">Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie` blok için bu değişikliği yapmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="94323-137">Tamamlanan [SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="94323-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="94323-138">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94323-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94323-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="94323-140">Derecelendirme alanı için geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="94323-140">Add a migration for the rating field</span></span>

<span data-ttu-id="94323-141">**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="94323-142">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="94323-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="94323-143">Komut `Add-Migration` çerçevesöyler:</span><span class="sxs-lookup"><span data-stu-id="94323-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="94323-144">`Movie` Modeli `Movie` DB şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="94323-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="94323-145">DB şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="94323-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="94323-146">"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94323-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="94323-147">Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="94323-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="94323-148">Komut, `Update-Database` şema değişikliklerini veritabanına uygulamak ve varolan verileri korumak için çerçeveyi söyler.</span><span class="sxs-lookup"><span data-stu-id="94323-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="94323-149">DB'deki tüm kayıtları silerseniz, baş harfe çevrilen `Rating` kullanıcı DB'yi tohumlar ve alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="94323-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="94323-150">Bunu tarayıcıdaki silme bağlantılarıyla veya [Sql Server Object Explorer'dan](xref:tutorials/razor-pages/sql#ssox) (SSOX) yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="94323-151">Başka bir seçenek veritabanını silmek ve veritabanını yeniden oluşturmak için geçişleri kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="94323-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="94323-152">SSOX veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="94323-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="94323-153">SSOX'taki veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="94323-154">Veritabanına sağ tıklayın ve *Sil'i*seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="94323-155">**Varolan bağlantıları kapat'ı**işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="94323-156">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-156">Select **OK**.</span></span>
* <span data-ttu-id="94323-157">[PMC'de](xref:tutorials/razor-pages/new-field#pmc)veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="94323-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94323-158">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94323-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="94323-159">Veritabanını bırakma ve yeniden oluşturma</span><span class="sxs-lookup"><span data-stu-id="94323-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="94323-160">Geçiş klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="94323-160">Delete the migration folder.</span></span>  <span data-ttu-id="94323-161">Veritabanını yeniden oluşturmak için aşağıdaki komutları kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="94323-162">Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğiniz/yönetebileceğinizi/görüntülediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="94323-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="94323-163">Veritabanı tohumlu değilse, `SeedData.Initialize` yöntemde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="94323-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94323-164">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94323-164">Additional resources</span></span>

* [<span data-ttu-id="94323-165">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="94323-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="94323-166">[Önceki:](xref:tutorials/razor-pages/search)
> Sonraki Arama[Ekleme: Doğrulama Ekleme](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="94323-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="94323-167">Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="94323-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="94323-168">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-168">Add a new field to the model.</span></span>
* <span data-ttu-id="94323-169">Yeni alan şeması değişikliğini veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="94323-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="94323-170">Otomatik olarak bir veritabanı oluşturmak için ÖNCE EF Code First'i kullanırken, Önce Kod:</span><span class="sxs-lookup"><span data-stu-id="94323-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="94323-171">Veritabanı şemasının oluşturulduğu model sınıflarıyla eşitlenip eşitolmadığını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="94323-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="94323-172">Model sınıfları DB ile eşit değilse, EF bir özel durum atar.</span><span class="sxs-lookup"><span data-stu-id="94323-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="94323-173">Şema/modelin eşitlenmiş olarak otomatik olarak doğrulanması, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="94323-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="94323-174">Film Modeline Derecelendirme Özelliği Ekleme</span><span class="sxs-lookup"><span data-stu-id="94323-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="94323-175">*Models/Movie.cs* dosyasını açın `Rating` ve bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="94323-176">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="94323-176">Build the app.</span></span>

<span data-ttu-id="94323-177">*Sayfaları/Filmleri/Index.cshtml'i*edin `Rating` ve alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="94323-178">Aşağıdaki sayfaları güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="94323-178">Update the following pages:</span></span>

* <span data-ttu-id="94323-179">`Rating` Alanı Sil ve Ayrıntılar sayfalarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="94323-180">[Create.cshtml'i](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) `Rating` bir alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="94323-181">`Rating` Alanı Edit Sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="94323-182">DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="94323-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="94323-183">Şimdi çalıştırılırsa, uygulama atar: `SqlException`</span><span class="sxs-lookup"><span data-stu-id="94323-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="94323-184">Bu hata, güncelleştirilmiş Film modeli sınıfının veritabanının Film tablosunun şemasından farklı olması nedeniyle kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="94323-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="94323-185">(Veritabanı tablosunda `Rating` sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="94323-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="94323-186">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="94323-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="94323-187">Varlık Çerçevesi'nin yeni model sınıfı şemasını kullanarak veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="94323-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="94323-188">Bu yaklaşım, geliştirme döngüsünün başlarında kullanışlıdır; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="94323-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="94323-189">Dezavantajı veritabanında varolan verileri kaybetmek olduğunu.</span><span class="sxs-lookup"><span data-stu-id="94323-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="94323-190">Bu yaklaşımı üretim veritabanında kullanmayın!</span><span class="sxs-lookup"><span data-stu-id="94323-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="94323-191">Şema değişikliklerinde DB'yi bırakmak ve veritabanını test verileriyle otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="94323-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="94323-192">Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="94323-193">Bu yaklaşımın avantajı verilerinizi saklamanızdır.</span><span class="sxs-lookup"><span data-stu-id="94323-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="94323-194">Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="94323-195">Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="94323-196">Bu öğretici için Kod İlk Geçişler'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="94323-197">`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="94323-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="94323-198">Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie` blok için bu değişikliği yapmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="94323-199">Tamamlanan [SeedData.cs dosyasına](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)bakın.</span><span class="sxs-lookup"><span data-stu-id="94323-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="94323-200">Çözümü derleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94323-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94323-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="94323-202">Derecelendirme alanı için geçiş ekleme</span><span class="sxs-lookup"><span data-stu-id="94323-202">Add a migration for the rating field</span></span>

<span data-ttu-id="94323-203">**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="94323-204">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="94323-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="94323-205">Komut `Add-Migration` çerçevesöyler:</span><span class="sxs-lookup"><span data-stu-id="94323-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="94323-206">`Movie` Modeli `Movie` DB şemasıyla karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="94323-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="94323-207">DB şemasını yeni modele geçirmek için kod oluşturun.</span><span class="sxs-lookup"><span data-stu-id="94323-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="94323-208">"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="94323-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="94323-209">Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="94323-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="94323-210">Komut, `Update-Database` şema değişikliklerini veritabanına uygulamak için çerçeveyi söyler.</span><span class="sxs-lookup"><span data-stu-id="94323-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="94323-211">DB'deki tüm kayıtları silerseniz, baş harfe çevrilen `Rating` kullanıcı DB'yi tohumlar ve alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="94323-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="94323-212">Bunu tarayıcıdaki silme bağlantılarıyla veya [Sql Server Object Explorer'dan](xref:tutorials/razor-pages/sql#ssox) (SSOX) yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="94323-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="94323-213">Başka bir seçenek veritabanını silmek ve veritabanını yeniden oluşturmak için geçişleri kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="94323-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="94323-214">SSOX veritabanını silmek için:</span><span class="sxs-lookup"><span data-stu-id="94323-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="94323-215">SSOX'taki veritabanını seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="94323-216">Veritabanına sağ tıklayın ve *Sil'i*seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="94323-217">**Varolan bağlantıları kapat'ı**işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="94323-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="94323-218">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="94323-218">Select **OK**.</span></span>
* <span data-ttu-id="94323-219">[PMC'de](xref:tutorials/razor-pages/new-field#pmc)veritabanını güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="94323-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="94323-220">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94323-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="94323-221">Veritabanını bırakma ve yeniden oluşturma</span><span class="sxs-lookup"><span data-stu-id="94323-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="94323-222">Veritabanını silin ve veritabanını yeniden oluşturmak için geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="94323-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="94323-223">Veritabanını silmek için veritabanı dosyasını silin (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="94323-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="94323-224">Sonra komutu çalıştırın: `ef database update`</span><span class="sxs-lookup"><span data-stu-id="94323-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="94323-225">Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğiniz/yönetebileceğinizi/görüntülediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="94323-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="94323-226">Veritabanı tohumlu değilse, `SeedData.Initialize` yöntemde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="94323-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94323-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="94323-227">Additional resources</span></span>

* [<span data-ttu-id="94323-228">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="94323-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="94323-229">[Önceki:](xref:tutorials/razor-pages/search)
> Sonraki Arama[Ekleme: Doğrulama Ekleme](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="94323-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
