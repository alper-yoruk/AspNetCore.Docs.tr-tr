---
title: ASP.NET Core MVC uygulamasına yeni bir alan ekleme
author: rick-anderson
description: Bir modele yeni bir alan eklemek ve bu değişikliği veritabanına geçirmek için Entity Framework Code First Migrations kullanmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 0cc708734726e5779951d63ac10d1b750b1cc2ac
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777078"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d7e13-103">ASP.NET Core MVC uygulamasına yeni bir alan ekleme</span><span class="sxs-lookup"><span data-stu-id="d7e13-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d7e13-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7e13-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7e13-105">Bu bölümde [Entity Framework](/ef/core/get-started/aspnetcore/new-db) için Code First Migrations kullanılır:</span><span class="sxs-lookup"><span data-stu-id="d7e13-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d7e13-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-106">Add a new field to the model.</span></span>
* <span data-ttu-id="d7e13-107">Yeni alanı veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="d7e13-108">EF Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Code First:</span><span class="sxs-lookup"><span data-stu-id="d7e13-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d7e13-109">Veritabanının şemasını izlemek için veritabanına tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="d7e13-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="d7e13-110">Veritabanının oluşturulduğu model sınıflarıyla eşitlenmiş olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="d7e13-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="d7e13-111">Bunlar eşitlenmiş değilse EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d7e13-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="d7e13-112">Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d7e13-113">Film modeline bir derecelendirme özelliği ekleyin</span><span class="sxs-lookup"><span data-stu-id="d7e13-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d7e13-114">`Rating` *Modeller/film. cs*'ye bir özellik ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d7e13-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d7e13-115">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="d7e13-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="d7e13-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e13-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="d7e13-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="d7e13-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d7e13-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7e13-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d7e13-119">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e13-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d7e13-120">Komut ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="d7e13-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="d7e13-121">`Movie` Sınıfa yeni bir alan eklediyseniz, bu yeni özelliğin dahil edilmesini sağlamak için bağlama beyaz listesini güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="d7e13-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="d7e13-122">*MoviesController.cs*içinde `[Bind]` , `Create` `Rating` özelliği dahil etmek için hem hem de `Edit` eylem yöntemlerinin özniteliğini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="d7e13-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="d7e13-123">Yeni `Rating` özelliği tarayıcı görünümünde görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="d7e13-124">*/Views/movies/Index.cshtml* dosyasını düzenleyin ve bir `Rating` alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d7e13-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="d7e13-125">*/Views/movies/Create.cshtml* ile bir `Rating` alanı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="d7e13-126">Visual Studio/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e13-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="d7e13-127">Önceki "form grubunu kopyalayabilir/yapıştırabilir" ve IntelliSense 'in alanları güncelleştirmenize yardımcı olmasına izin verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="d7e13-128">IntelliSense, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile birlikte çalışmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Geliştirici, görünümün ikinci Label öğesinde için ASP-for öznitelik değeri için R harfini yazmıştır.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d7e13-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d7e13-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="d7e13-133">Kalan şablonları güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-133">Update the remaining templates.</span></span>

<span data-ttu-id="d7e13-134">`SeedData` Sınıfını yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d7e13-135">Aşağıda örnek bir değişiklik gösterilmektedir, ancak her biri `new Movie`için bu değişikliği yapmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="d7e13-136">VERITABANı yeni alanı içerecek şekilde güncelleştirilene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d7e13-137">Şimdi çalıştırıldığında, şunlar `SqlException` oluşur:</span><span class="sxs-lookup"><span data-stu-id="d7e13-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d7e13-138">Bu hata, güncelleştirilmiş film modeli sınıfı varolan veritabanının film tablosunun şemasından farklı olduğu için oluşur.</span><span class="sxs-lookup"><span data-stu-id="d7e13-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="d7e13-139">(Veritabanı tablosunda sütun `Rating` yok.)</span><span class="sxs-lookup"><span data-stu-id="d7e13-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d7e13-140">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="d7e13-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d7e13-141">Entity Framework yeni model sınıfı şemasına göre otomatik olarak veritabanını bırakıp yeniden oluşturmayı sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="d7e13-142">Bu yaklaşım, bir test veritabanı üzerinde etkin geliştirme yaparken geliştirme döngüsünün başlarında çok daha kolay. modeli ve veritabanı şemasını birlikte hızla gelişmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d7e13-143">Bunun yanında, bu yaklaşımı bir üretim veritabanında kullanmak istemezsiniz, ancak bu, veritabanında var olan verileri kaybetmeniz olur.</span><span class="sxs-lookup"><span data-stu-id="d7e13-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="d7e13-144">Bir veritabanının test verileriyle otomatik olarak çekirdeği oluşturmak için bir başlatıcı kullanılması, genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="d7e13-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="d7e13-145">Bu, erken geliştirme ve SQLite kullanılırken iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="d7e13-146">Mevcut veritabanının şemasını model sınıflarıyla eşleşecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d7e13-147">Bu yaklaşımın avantajı, verilerinizi tutmanızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d7e13-148">Bu değişikliği el ile ya da bir veritabanı değişiklik betiği oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d7e13-149">Veritabanı şemasını güncelleştirmek için Code First Migrations kullanın.</span><span class="sxs-lookup"><span data-stu-id="d7e13-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d7e13-150">Bu öğretici için Code First Migrations kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d7e13-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e13-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7e13-152">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu**' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC menüsü](adding-model/_static/pmc.png)

<span data-ttu-id="d7e13-154">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="d7e13-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d7e13-155">Bu `Add-Migration` komut, geçiş çerçevesinin geçerli `Movie` `Movie` DB şemasıyla geçerli modeli incelemesini ve veritabanını yeni modele geçirmek için gerekli kodu oluşturmasını söyler.</span><span class="sxs-lookup"><span data-stu-id="d7e13-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="d7e13-156">"Derecelendirme" adı rastgele olur ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d7e13-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d7e13-157">Geçiş dosyası için anlamlı bir ad kullanılması yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="d7e13-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d7e13-158">VERITABANıNDAKI tüm kayıtlar silinirse, Initialize yöntemi VERITABANıNı temel alır ve `Rating` alanını içerir.</span><span class="sxs-lookup"><span data-stu-id="d7e13-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d7e13-159">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e13-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d7e13-160">Veritabanını silin ve geçişleri kullanarak veritabanını yeniden oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d7e13-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d7e13-161">Veritabanını silmek için veritabanı dosyasını (*Mvcmovie. db*) silin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d7e13-162">Ardından şu `ef database update` komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d7e13-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="d7e13-163">Uygulamayı çalıştırın ve bir alan ile film oluşturabileceğiniz, düzenleyebileceğiniz ve görüntüleyebilen bir `Rating` doğrulama yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d7e13-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span> <span data-ttu-id="d7e13-164">Uygulamayı güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="d7e13-164">Update the app:</span></span>

* <span data-ttu-id="d7e13-165">`Rating` Alanı `Edit`, `Details`, ve `Delete` görüntüleme şablonlarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d7e13-165">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>
* <span data-ttu-id="d7e13-166">' Nin düzenleme eylemi yönteminde bağlamayı güncelleştirin `MoviesController`.</span><span class="sxs-lookup"><span data-stu-id="d7e13-166">Update the binding in the edit action method of the `MoviesController`.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d7e13-167">[Önceki](search.md)
> [İleri](validation.md)</span><span class="sxs-lookup"><span data-stu-id="d7e13-167">[Previous](search.md)
[Next](validation.md)</span></span>
