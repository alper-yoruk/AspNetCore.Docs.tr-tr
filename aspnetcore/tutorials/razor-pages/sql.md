---
title: 4. bölüm, bir veritabanıyla çalışma
author: rick-anderson
description: Sayfalardaki eğitim serisinin 4. bölümü Razor .
ms.author: riande
ms.date: 09/26/2020
ms.custom: contperf-fy21q2
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 552a74016c281af248ce735c6ed6a5a55768a16a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486239"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="04748-103">Sayfalardaki Eğitim Serisi Bölüm 4 Razor</span><span class="sxs-lookup"><span data-stu-id="04748-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="04748-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="04748-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="04748-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="04748-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="04748-106">`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="04748-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="04748-107">Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : </span><span class="sxs-lookup"><span data-stu-id="04748-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-109">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="04748-110">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur.</span><span class="sxs-lookup"><span data-stu-id="04748-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="04748-111">Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.</span><span class="sxs-lookup"><span data-stu-id="04748-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="04748-113">Oluşturulan bağlantı dizesi aşağıdakine benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="04748-113">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-114">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="04748-115">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="04748-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="04748-116">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="04748-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="04748-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="04748-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="04748-119">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="04748-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="04748-120">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="04748-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="04748-121">Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:\Users\<user>\` .</span><span class="sxs-lookup"><span data-stu-id="04748-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="04748-122">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="04748-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Görünüm menüsü](sql/_static/5/ssox.png)

1. <span data-ttu-id="04748-124">Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Film tablosunda açık bağlamsal menüler](sql/_static/5/design.png)

   ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

   <span data-ttu-id="04748-127">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="04748-128">Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="04748-129">Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-131">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="04748-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="04748-132">SQLite</span></span>

<span data-ttu-id="04748-133">[SQLite](https://www.sqlite.org/) Web sitesi şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="04748-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="04748-134">SQLite, kendinden bağımsız, yüksek güvenilirlik, gömülü, tam özellikli, genel etki alanı, SQL veritabanı altyapısı.</span><span class="sxs-lookup"><span data-stu-id="04748-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="04748-135">SQLite, dünyanın en çok kullanılan veritabanı altyapısıdır.</span><span class="sxs-lookup"><span data-stu-id="04748-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="04748-136">Bir SQLite veritabanını yönetmek ve görüntülemek için indirebileceğiniz birçok üçüncü taraf araç vardır.</span><span class="sxs-lookup"><span data-stu-id="04748-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="04748-137">Aşağıdaki görüntü, [SQLite Için veritabanı tarayıcısıdır](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="04748-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="04748-138">En sevdiğiniz bir SQLite aracınız varsa, onunla ilgili olarak istediğiniz gibi bir yorum bırakın.</span><span class="sxs-lookup"><span data-stu-id="04748-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Film veritabanını gösteren SQLite için DB tarayıcısı](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="04748-140">Bu öğreticide, Entity Framework Core *geçişleri* özelliği mümkün olan yerlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04748-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="04748-141">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="04748-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="04748-142">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="04748-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="04748-143">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="04748-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="04748-144">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="04748-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="04748-145">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="04748-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="04748-146">Bunun yerine, şema değiştiğinde veritabanı bırakılır ve yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="04748-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="04748-147">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04748-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="04748-148">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="04748-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="04748-149">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="04748-149">Creating a new table.</span></span>
>* <span data-ttu-id="04748-150">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="04748-151">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-151">Dropping the old table.</span></span>
>* <span data-ttu-id="04748-152">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-152">Renaming the new table.</span></span>
>
><span data-ttu-id="04748-153">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="04748-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="04748-154">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="04748-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="04748-155">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="04748-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="04748-156">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="04748-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="04748-157">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="04748-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="04748-158">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="04748-158">Seed the database</span></span>

<span data-ttu-id="04748-159">`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="04748-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="04748-160">Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="04748-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="04748-161">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="04748-161">Add the seed initializer</span></span>

<span data-ttu-id="04748-162">*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="04748-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="04748-163">Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="04748-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="04748-164">Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.</span><span class="sxs-lookup"><span data-stu-id="04748-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="04748-165">Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.</span><span class="sxs-lookup"><span data-stu-id="04748-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="04748-166">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="04748-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="04748-167">[Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="04748-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="04748-168">Çalıştırılmayan aşağıdaki özel durum oluşur `Update-Database` :</span><span class="sxs-lookup"><span data-stu-id="04748-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="04748-169">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="04748-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="04748-171">Veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-171">Delete all the records in the database.</span></span> <span data-ttu-id="04748-172">Tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarını kullanma</span><span class="sxs-lookup"><span data-stu-id="04748-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="04748-173">Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır.</span><span class="sxs-lookup"><span data-stu-id="04748-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="04748-174">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="04748-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="04748-175">Aşağıdaki yaklaşımlardan biriyle IIS 'yi durdurun ve yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="04748-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="04748-176">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' u seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Bağlamsal menü](sql/_static/stopIIS.png)

   1. <span data-ttu-id="04748-179">Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="04748-180">Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-181">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="04748-182">Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="04748-183">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="04748-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="04748-184">Uygulama, sağlanan verileri gösterir:</span><span class="sxs-lookup"><span data-stu-id="04748-184">The app shows the seeded data:</span></span>

![Film verilerini gösteren tarayıcıda açık film uygulaması](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="04748-186">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="04748-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="04748-187">[Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="04748-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="04748-188">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="04748-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="04748-189">`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="04748-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="04748-190">Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : </span><span class="sxs-lookup"><span data-stu-id="04748-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-192">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="04748-193">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur.</span><span class="sxs-lookup"><span data-stu-id="04748-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="04748-194">Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.</span><span class="sxs-lookup"><span data-stu-id="04748-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="04748-196">Oluşturulan bağlantı dizesi aşağıdakine benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="04748-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-197">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="04748-198">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="04748-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="04748-199">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="04748-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="04748-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="04748-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="04748-202">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="04748-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="04748-203">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="04748-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="04748-204">Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:\Users\<user>\` .</span><span class="sxs-lookup"><span data-stu-id="04748-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="04748-205">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="04748-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="04748-207">Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Film tablosunda açık bağlamsal menüler](sql/_static/design.png)

  ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

<span data-ttu-id="04748-210">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="04748-211">Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="04748-212">Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-214">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="04748-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="04748-215">SQLite</span></span>

<span data-ttu-id="04748-216">[SQLite](https://www.sqlite.org/) Web sitesi şunları belirtir:</span><span class="sxs-lookup"><span data-stu-id="04748-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="04748-217">SQLite, kendinden bağımsız, yüksek güvenilirlik, gömülü, tam özellikli, genel etki alanı, SQL veritabanı altyapısı.</span><span class="sxs-lookup"><span data-stu-id="04748-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="04748-218">SQLite, dünyanın en çok kullanılan veritabanı altyapısıdır.</span><span class="sxs-lookup"><span data-stu-id="04748-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="04748-219">Bir SQLite veritabanını yönetmek ve görüntülemek için indirebileceğiniz birçok üçüncü taraf araç vardır.</span><span class="sxs-lookup"><span data-stu-id="04748-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="04748-220">Aşağıdaki görüntü, [SQLite Için veritabanı tarayıcısıdır](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="04748-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="04748-221">En sevdiğiniz bir SQLite aracınız varsa, onunla ilgili olarak istediğiniz gibi bir yorum bırakın.</span><span class="sxs-lookup"><span data-stu-id="04748-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![Film veritabanını gösteren SQLite için DB tarayıcısı](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="04748-223">Bu öğreticide, Entity Framework Core *geçişleri* özelliği mümkün olan yerlerde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04748-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="04748-224">Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="04748-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="04748-225">Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="04748-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="04748-226">Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="04748-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="04748-227">Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="04748-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="04748-228">Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="04748-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="04748-229">Bunun yerine, şema değiştiğinde veritabanı bırakılır ve yeniden oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="04748-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="04748-230">SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04748-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="04748-231">Tablo yeniden oluşturma şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="04748-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="04748-232">Yeni bir tablo oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="04748-232">Creating a new table.</span></span>
>* <span data-ttu-id="04748-233">Eski tablodaki veriler yeni tabloya kopyalanıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="04748-234">Eski tablo bırakılıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-234">Dropping the old table.</span></span>
>* <span data-ttu-id="04748-235">Yeni tablo yeniden adlandırılıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-235">Renaming the new table.</span></span>
>
><span data-ttu-id="04748-236">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="04748-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="04748-237">SQLite EF Core veritabanı sağlayıcısı sınırlamaları</span><span class="sxs-lookup"><span data-stu-id="04748-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="04748-238">Geçiş kodunu özelleştirme</span><span class="sxs-lookup"><span data-stu-id="04748-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="04748-239">Veri çekirdeği oluşturma</span><span class="sxs-lookup"><span data-stu-id="04748-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="04748-240">SQLite ALTER TABLE ifadesi</span><span class="sxs-lookup"><span data-stu-id="04748-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="04748-241">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="04748-241">Seed the database</span></span>

<span data-ttu-id="04748-242">`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="04748-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="04748-243">Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="04748-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="04748-244">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="04748-244">Add the seed initializer</span></span>

<span data-ttu-id="04748-245">*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="04748-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="04748-246">Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="04748-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="04748-247">Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.</span><span class="sxs-lookup"><span data-stu-id="04748-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="04748-248">Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.</span><span class="sxs-lookup"><span data-stu-id="04748-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="04748-249">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="04748-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="04748-250">[Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="04748-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="04748-251">Çalıştırılmayan aşağıdaki özel durum oluşur `Update-Database` :</span><span class="sxs-lookup"><span data-stu-id="04748-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="04748-252">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="04748-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04748-254">Veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-254">Delete all the records in the database.</span></span> <span data-ttu-id="04748-255">Tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox)'ten silme bağlantılarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="04748-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="04748-256">Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır.</span><span class="sxs-lookup"><span data-stu-id="04748-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="04748-257">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="04748-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="04748-258">Aşağıdaki yaklaşımlardan biriyle IIS 'yi durdurun ve yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="04748-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="04748-259">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:</span><span class="sxs-lookup"><span data-stu-id="04748-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="04748-262">Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="04748-263">Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-264">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="04748-265">Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="04748-266">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="04748-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="04748-267">Uygulama, sağlanan verileri gösterir:</span><span class="sxs-lookup"><span data-stu-id="04748-267">The app shows the seeded data:</span></span>

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="04748-269">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="04748-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="04748-270">[Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="04748-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04748-271">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="04748-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="04748-272">`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="04748-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="04748-273">Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : </span><span class="sxs-lookup"><span data-stu-id="04748-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="04748-275">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="04748-276">İçinde kullanılan yöntemler hakkında daha fazla bilgi için `ConfigureServices` bkz.:</span><span class="sxs-lookup"><span data-stu-id="04748-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="04748-277">[ASP.NET Core Için ab genel veri koruma yönetmeliği (GDPR) desteği](xref:security/gdpr) `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="04748-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="04748-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="04748-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="04748-279">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur.</span><span class="sxs-lookup"><span data-stu-id="04748-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="04748-280">Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.</span><span class="sxs-lookup"><span data-stu-id="04748-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="04748-282">Oluşturulan bağlantı dizesi aşağıdakine benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="04748-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="04748-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04748-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04748-284">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="04748-285">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="04748-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="04748-286">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="04748-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="04748-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="04748-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="04748-289">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="04748-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="04748-290">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="04748-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="04748-291">Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:/Users/<user/>` .</span><span class="sxs-lookup"><span data-stu-id="04748-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="04748-292">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="04748-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="04748-294">Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Film tablosunda bağlam menüsü açık](sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](sql/_static/dv.png)

<span data-ttu-id="04748-297">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="04748-298">Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .</span><span class="sxs-lookup"><span data-stu-id="04748-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="04748-299">Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="04748-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="04748-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04748-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04748-302">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="04748-303">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="04748-303">Seed the database</span></span>

<span data-ttu-id="04748-304">`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="04748-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="04748-305">Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="04748-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="04748-306">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="04748-306">Add the seed initializer</span></span>

<span data-ttu-id="04748-307">*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="04748-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="04748-308">Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="04748-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="04748-309">Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.</span><span class="sxs-lookup"><span data-stu-id="04748-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="04748-310">Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.</span><span class="sxs-lookup"><span data-stu-id="04748-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="04748-311">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="04748-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="04748-312">[Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="04748-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="04748-313">Bir üretim uygulaması çağırmaz `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="04748-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="04748-314">Çalıştırılmayan aşağıdaki özel durumu engellemek için önceki koda eklenir `Update-Database` :</span><span class="sxs-lookup"><span data-stu-id="04748-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="04748-315">SqlException: Razor oturum açma tarafından istenen "pagesmoviecontext-21" veritabanı açılamıyor.</span><span class="sxs-lookup"><span data-stu-id="04748-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="04748-316">Oturum açılamadı.</span><span class="sxs-lookup"><span data-stu-id="04748-316">The login failed.</span></span>
<span data-ttu-id="04748-317">' Kullanıcı adı ' kullanıcısı için oturum açma başarısız.</span><span class="sxs-lookup"><span data-stu-id="04748-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="04748-318">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="04748-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04748-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="04748-320">Veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-320">Delete all the records in the database.</span></span> <span data-ttu-id="04748-321">Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="04748-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="04748-322">Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır.</span><span class="sxs-lookup"><span data-stu-id="04748-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="04748-323">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="04748-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="04748-324">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="04748-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="04748-325">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:</span><span class="sxs-lookup"><span data-stu-id="04748-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="04748-328">Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="04748-329">Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="04748-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="04748-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="04748-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="04748-331">Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="04748-332">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="04748-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04748-333">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04748-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="04748-334">Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="04748-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="04748-335">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="04748-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="04748-336">Uygulama, sağlanan verileri gösterir:</span><span class="sxs-lookup"><span data-stu-id="04748-336">The app shows the seeded data:</span></span>

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55https.png)

<span data-ttu-id="04748-338">Sonraki öğretici, verilerin sunumunu temizler.</span><span class="sxs-lookup"><span data-stu-id="04748-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04748-339">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="04748-339">Additional resources</span></span>

* [<span data-ttu-id="04748-340">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="04748-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="04748-341">[Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="04748-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
