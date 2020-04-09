---
title: ASP.NET Core MVC uygulamasına yeni bir alan ekleme
author: rick-anderson
description: Bir modele yeni bir alan eklemek ve bu değişikliği veritabanına geçirmek için Varlık Çerçeve Kodu İlk Geçişleri'ni nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662906"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="dd9ca-103">ASP.NET Core MVC uygulamasına yeni bir alan ekleme</span><span class="sxs-lookup"><span data-stu-id="dd9ca-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="dd9ca-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dd9ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dd9ca-105">Bu bölümde [Varlık Çerçeve](/ef/core/get-started/aspnetcore/new-db) Kodu İlk Geçişler için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="dd9ca-106">Modele yeni bir alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-106">Add a new field to the model.</span></span>
* <span data-ttu-id="dd9ca-107">Yeni alanı veritabanına geçirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="dd9ca-108">Ef Code First otomatik olarak bir veritabanı oluşturmak için kullanıldığında, Önce Kod:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="dd9ca-109">Veritabanının şemasını izlemek için veritabanına bir tablo ekler.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="dd9ca-110">Veritabanının oluşturulduğu model sınıflarıyla eşit olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="dd9ca-111">Eşitlenmemişlerse, EF bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="dd9ca-112">Bu, tutarsız veritabanı/kod sorunlarını bulmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="dd9ca-113">Film Modeline Derecelendirme Özelliği Ekleme</span><span class="sxs-lookup"><span data-stu-id="dd9ca-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="dd9ca-114">`Rating` *Modellere/Movie.cs'ye*özellik ekle :</span><span class="sxs-lookup"><span data-stu-id="dd9ca-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="dd9ca-115">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="dd9ca-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="dd9ca-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd9ca-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="dd9ca-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="dd9ca-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="dd9ca-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd9ca-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd9ca-119">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd9ca-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dd9ca-120">Komut + B</span><span class="sxs-lookup"><span data-stu-id="dd9ca-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="dd9ca-121">`Movie` Sınıfa yeni bir alan eklediğiniz için, bu yeni özelliğin eklenmesi için bağlayıcı beyaz listeyi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="dd9ca-122">*MoviesController.cs,* hem `[Bind]` özellik `Create` hem de `Edit` eylem yöntemleri için `Rating` özniteliği güncelleştirmeözelliği içerecek şekilde:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="dd9ca-123">Tarayıcı görünümünde yeni `Rating` özelliği görüntülemek, oluşturmak ve düzenlemek için görünüm şablonlarını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="dd9ca-124">*/Views/Movies/Index.cshtml* dosyasını edin `Rating` ve bir alan ekleyin:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="dd9ca-125">*/Views/Movies/Create.cshtml'i* bir `Rating` alanla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="dd9ca-126">Mac için Visual Studio / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd9ca-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="dd9ca-127">Önceki "form grubunu" kopyalayabilir/yapıştırabilir ve intelliSense'in alanları güncelleştirmenize yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="dd9ca-128">IntelliSense [Tag Helpers](xref:mvc/views/tag-helpers/intro)ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Geliştirici, görünümün ikinci etiket öğesine asp-for öznitelik değeri için R harfini yazdı.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd9ca-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd9ca-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="dd9ca-133">Kalan şablonları güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-133">Update the remaining templates.</span></span>

<span data-ttu-id="dd9ca-134">`SeedData` Sınıfı, yeni sütun için bir değer sağlayacak şekilde güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="dd9ca-135">Örnek bir değişiklik aşağıda gösterilmiştir, ancak her `new Movie`biri için bu değişikliği yapmak isteyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="dd9ca-136">DB yeni alanı içerecek şekilde güncellenene kadar uygulama çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="dd9ca-137">Şimdi çalıştırılırsa, aşağıdakiler `SqlException` atılır:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="dd9ca-138">Güncelleştirilmiş Film modeli sınıfı varolan veritabanının Film tablosunun şemasından farklı olduğundan bu hata oluşur.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="dd9ca-139">(Veritabanı tablosunda `Rating` sütun yok.)</span><span class="sxs-lookup"><span data-stu-id="dd9ca-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="dd9ca-140">Hatayı çözmek için birkaç yaklaşım vardır:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="dd9ca-141">Varlık Çerçevesi'nin yeni model sınıfı şemasına dayalı veritabanını otomatik olarak düşürmesini ve yeniden oluşturmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="dd9ca-142">Bu yaklaşım, bir test veritabanında etkin geliştirme yaparken geliştirme döngüsünün başlarında çok uygundur; modeli ve veritabanı şemasını birlikte hızla geliştirmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="dd9ca-143">Dezavantajı olsa da, veritabanında mevcut verileri kaybetmek olduğunu - bu yüzden bir üretim veritabanında bu yaklaşımı kullanmak istemiyorum!</span><span class="sxs-lookup"><span data-stu-id="dd9ca-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="dd9ca-144">Test verileriyle bir veritabanını otomatik olarak tohumlamak için bir baş harf kullanmak genellikle bir uygulama geliştirmenin üretken bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="dd9ca-145">Bu erken gelişim için iyi bir yaklaşımdır ve SQLite kullanırken.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="dd9ca-146">Varolan veritabanının şemasını model sınıflarıyla eşleşebilecek şekilde açıkça değiştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="dd9ca-147">Bu yaklaşımın avantajı verilerinizi saklamanızdır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="dd9ca-148">Bu değişikliği el ile veya veritabanı değişikliği komut dosyası oluşturarak yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="dd9ca-149">Veritabanı şemasını güncelleştirmek için Kod İlk Geçişler'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="dd9ca-150">Bu öğretici için, Kod İlk Geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd9ca-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd9ca-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd9ca-152">**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC menüsü](adding-model/_static/pmc.png)

<span data-ttu-id="dd9ca-154">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="dd9ca-155">Komut, `Add-Migration` geçerli `Movie` `Movie` DB şeması yla geçerli modeli incelemesi ve DB'yi yeni modele geçirmek için gerekli kodu oluşturmasını geçiş çerçevesine söyler.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="dd9ca-156">"Derecelendirme" adı rasgeledir ve geçiş dosyasını adlandırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="dd9ca-157">Geçiş dosyası için anlamlı bir ad kullanmak yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="dd9ca-158">DB'deki tüm kayıtlar silinirse, başharf yöntemi DB'yi `Rating` tohumlar ve alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd9ca-159">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd9ca-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="dd9ca-160">Veritabanını silin ve veritabanını yeniden oluşturmak için geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="dd9ca-161">Veritabanını silmek için veritabanı dosyasını silin (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="dd9ca-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="dd9ca-162">Sonra komutu çalıştırın: `ef database update`</span><span class="sxs-lookup"><span data-stu-id="dd9ca-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="dd9ca-163">Uygulamayı çalıştırın ve bir `Rating` alanla film oluşturabileceğinizi, edinebileceğinizi ve görüntülediğinizi doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span> <span data-ttu-id="dd9ca-164">Uygulamayı güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="dd9ca-164">Update the app:</span></span>

* <span data-ttu-id="dd9ca-165">`Rating` Alanı `Edit`, , `Details`ve `Delete` görünüm şablonlarına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-165">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>
* <span data-ttu-id="dd9ca-166">'nin edit eylem yöntemindeki `MoviesController`bağlamayı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="dd9ca-166">Update the binding in the edit action method of the `MoviesController`.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dd9ca-167">[Önceki](search.md)
> [Sonraki](validation.md)</span><span class="sxs-lookup"><span data-stu-id="dd9ca-167">[Previous](search.md)
[Next](validation.md)</span></span>
