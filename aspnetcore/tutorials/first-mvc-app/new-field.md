---
title: 8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin
author: rick-anderson
description: ASP.NET Core MVC 'de öğretici serisinin 8. bölümü.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422541"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="64041-103">8. bölüm, ASP.NET Core MVC uygulamasına yeni bir alan ekleyin</span><span class="sxs-lookup"><span data-stu-id="64041-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="64041-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64041-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64041-105">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="64041-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="64041-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="64041-106">Add a new field to the model.</span></span>
* <span data-ttu-id="64041-107">Yeni alanı veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="64041-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="64041-108">EF Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Code First:</span><span class="sxs-lookup"><span data-stu-id="64041-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="64041-109">Veritabanının şemasını izlemek için veritabanına tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="64041-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="64041-110">Veritabanının oluşturulduğu model sınıflarıyla eşitlenmiş olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="64041-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="64041-111">Bunlar eşitlenmiş değilse EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="64041-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="64041-112">Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="64041-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="64041-113">Film modeline bir derecelendirme özelliği ekleyin</span><span class="sxs-lookup"><span data-stu-id="64041-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="64041-114">`Rating` *Modeller/film. cs* 'ye bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64041-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

<span data-ttu-id="64041-115">Uygulamayı oluşturma</span><span class="sxs-lookup"><span data-stu-id="64041-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="64041-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64041-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="64041-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="64041-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="64041-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64041-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64041-119">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64041-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="64041-120">Komut ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="64041-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="64041-121">Sınıfa yeni bir alan eklediğiniz için `Movie` Özellik bağlama listesini güncelleştirmeniz gerekir, bu nedenle bu yeni özellik dahil edilecek.</span><span class="sxs-lookup"><span data-stu-id="64041-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="64041-122">*MoviesController.cs* içinde, `[Bind]` `Create` `Edit` özelliği dahil etmek için hem hem de eylem yöntemlerinin özniteliğini güncelleştirin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="64041-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="64041-123">Yeni özelliği tarayıcı görünümünde görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin `Rating` .</span><span class="sxs-lookup"><span data-stu-id="64041-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="64041-124">*/Views/movies/Index.cshtml* dosyasını düzenleyin ve bir alan ekleyin `Rating` :</span><span class="sxs-lookup"><span data-stu-id="64041-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

<span data-ttu-id="64041-125">*/Views/movies/Create.cshtml* ile bir alanı güncelleştirin `Rating` .</span><span class="sxs-lookup"><span data-stu-id="64041-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="64041-126">Visual Studio/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64041-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="64041-127">Önceki "form grubunu kopyalayabilir/yapıştırabilir" ve IntelliSense 'in alanları güncelleştirmenize yardımcı olmasına izin verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64041-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="64041-128">IntelliSense, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="64041-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Geliştirici, görünümün ikinci Label öğesinde için ASP-for öznitelik değeri için R harfini yazmıştır.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="64041-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64041-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="64041-133">Kalan şablonları güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="64041-133">Update the remaining templates.</span></span>

<span data-ttu-id="64041-134">`SeedData`Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="64041-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="64041-135">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her biri için bu değişikliği yapmak isteyeceksiniz `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="64041-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="64041-136">VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="64041-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="64041-137">Şimdi çalıştırıldığında, şunlar `SqlException` oluşur:</span><span class="sxs-lookup"><span data-stu-id="64041-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="64041-138">Bu hata, güncelleştirilmiş film modeli sınıfı varolan veritabanının film tablosunun şemasından farklı olduğu için oluşur.</span><span class="sxs-lookup"><span data-stu-id="64041-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="64041-139">( `Rating` Veritabanı tablosunda sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="64041-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="64041-140">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="64041-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="64041-141">Entity Framework yeni model sınıfı şemasına göre otomatik olarak veritabanını bırakıp yeniden oluşturmayı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64041-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="64041-142">Bu yaklaşım, bir test veritabanı üzerinde etkin geliştirme yaparken geliştirme döngüsünün başlarında çok daha kolay. modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="64041-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="64041-143">Bunun yanında, bu yaklaşımı bir üretim veritabanında kullanmak istemezsiniz, ancak bu, veritabanında var olan verileri kaybetmeniz olur.</span><span class="sxs-lookup"><span data-stu-id="64041-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="64041-144">Bir veritabanının test verileriyle otomatik olarak çekirdeği oluşturmak için bir başlatıcı kullanılması, genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="64041-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="64041-145">Bu, erken geliştirme ve SQLite kullanılırken iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="64041-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="64041-146">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="64041-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="64041-147">Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="64041-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="64041-148">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64041-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="64041-149">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="64041-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="64041-150">Bu öğretici için Code First Migrations kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64041-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64041-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64041-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64041-152">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="64041-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC menüsü](adding-model/_static/pmc.png)

<span data-ttu-id="64041-154">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="64041-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="64041-155">Bu `Add-Migration` komut, geçiş çerçevesinin geçerli `Movie` DB şemasıyla geçerli modeli INCELEMESINI `Movie` ve veritabanını yeni modele geçirmek için gerekli kodu oluşturmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="64041-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="64041-156">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64041-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="64041-157">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="64041-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="64041-158">VERITABANıNDAKI tüm kayıtlar silinirse, Initialize yöntemi VERITABANıNı temel alır ve `Rating` alanını içerir.</span><span class="sxs-lookup"><span data-stu-id="64041-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64041-159">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64041-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="64041-160">Veritabanını ve önceki geçişini silip, veritabanını yeniden oluşturmak için geçişleri kullanın:</span><span class="sxs-lookup"><span data-stu-id="64041-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="64041-161">`dotnet ef migrations remove` Son geçişi kaldırır.</span><span class="sxs-lookup"><span data-stu-id="64041-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="64041-162">Birden fazla geçiş varsa, geçişler klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="64041-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="64041-163">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz, düzenleyebileceğiniz ve görüntüleyebilen bir doğrulama yapabilirsiniz `Rating` .</span><span class="sxs-lookup"><span data-stu-id="64041-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="64041-164">[Önceki](search.md) 
>  [Sonraki](validation.md)</span><span class="sxs-lookup"><span data-stu-id="64041-164">[Previous](search.md)
[Next](validation.md)</span></span>
