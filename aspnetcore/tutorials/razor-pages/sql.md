---
title: 4. bölüm, bir veritabanı ve ASP.NET Core
author: rick-anderson
description: Sayfalardaki eğitim serisinin 4. bölümü Razor .
ms.author: riande
ms.date: 7/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 21ae2ed4e91a0b3e52b1cdad1f4f4686c50614ba
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652979"
---
# <a name="part-4-with-a-database-and-aspnet-core"></a><span data-ttu-id="41eb5-103">4. bölüm, bir veritabanı ve ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41eb5-103">Part 4, with a database and ASP.NET Core</span></span>

<span data-ttu-id="41eb5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="41eb5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="41eb5-105">`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="41eb5-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="41eb5-106">Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="41eb5-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-108">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="41eb5-109">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="41eb5-110">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır.</span><span class="sxs-lookup"><span data-stu-id="41eb5-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41eb5-112">Veritabanı () için ad değeri, `Database={Database name}` üretilen kodunuz için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="41eb5-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="41eb5-113">Ad değeri rastgele.</span><span class="sxs-lookup"><span data-stu-id="41eb5-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-114">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="41eb5-115">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="41eb5-116">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="41eb5-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="41eb5-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="41eb5-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="41eb5-119">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="41eb5-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="41eb5-120">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="41eb5-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="41eb5-121">Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:\Users\<user>\` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="41eb5-122">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="41eb5-124">Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Film tablosunda açık bağlamsal menüler](sql/_static/design.png)

  ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

<span data-ttu-id="41eb5-127">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="41eb5-128">Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="41eb5-129">Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-131">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="41eb5-132">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="41eb5-132">Seed the database</span></span>

<span data-ttu-id="41eb5-133">`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="41eb5-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="41eb5-134">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="41eb5-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="41eb5-135">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="41eb5-135">Add the seed initializer</span></span>

<span data-ttu-id="41eb5-136">*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="41eb5-137">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="41eb5-138">Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.</span><span class="sxs-lookup"><span data-stu-id="41eb5-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="41eb5-139">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="41eb5-140">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="41eb5-141">Çalıştırılmayan aşağıdaki özel durum oluşur `Update-Database` :</span><span class="sxs-lookup"><span data-stu-id="41eb5-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="41eb5-142">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="41eb5-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41eb5-144">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="41eb5-144">Delete all the records in the DB.</span></span> <span data-ttu-id="41eb5-145">Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="41eb5-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="41eb5-146">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (sınıftaki yöntemleri çağırın `Startup` ).</span><span class="sxs-lookup"><span data-stu-id="41eb5-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="41eb5-147">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="41eb5-148">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="41eb5-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="41eb5-149">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:</span><span class="sxs-lookup"><span data-stu-id="41eb5-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="41eb5-152">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="41eb5-153">Ile hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-154">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="41eb5-155">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="41eb5-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="41eb5-156">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="41eb5-157">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="41eb5-158">Sonraki öğreticide, verilerin sunumu gelişmeyecektir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41eb5-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="41eb5-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="41eb5-160">[Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="41eb5-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="41eb5-161">`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="41eb5-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="41eb5-162">Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="41eb5-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-164">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="41eb5-165">İçinde kullanılan yöntemler hakkında daha fazla bilgi için `ConfigureServices` bkz.:</span><span class="sxs-lookup"><span data-stu-id="41eb5-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="41eb5-166">[ASP.NET Core Için ab genel veri koruma yönetmeliği (GDPR) desteği](xref:security/gdpr) `CookiePolicyOptions` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="41eb5-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="41eb5-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="41eb5-168">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="41eb5-169">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır.</span><span class="sxs-lookup"><span data-stu-id="41eb5-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41eb5-171">Veritabanı () için ad değeri, `Database={Database name}` üretilen kodunuz için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="41eb5-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="41eb5-172">Ad değeri rastgele.</span><span class="sxs-lookup"><span data-stu-id="41eb5-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="41eb5-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41eb5-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-174">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="41eb5-175">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="41eb5-176">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="41eb5-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="41eb5-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="41eb5-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="41eb5-179">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="41eb5-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="41eb5-180">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="41eb5-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="41eb5-181">Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:/Users/<user/>` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="41eb5-182">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="41eb5-184">Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Film tablosunda bağlam menüsü açık](sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](sql/_static/dv.png)

<span data-ttu-id="41eb5-187">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="41eb5-188">Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="41eb5-189">Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="41eb5-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41eb5-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-192">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="41eb5-193">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="41eb5-193">Seed the database</span></span>

<span data-ttu-id="41eb5-194">`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="41eb5-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="41eb5-195">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="41eb5-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="41eb5-196">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="41eb5-196">Add the seed initializer</span></span>

<span data-ttu-id="41eb5-197">*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="41eb5-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="41eb5-198">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="41eb5-199">Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.</span><span class="sxs-lookup"><span data-stu-id="41eb5-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="41eb5-200">Çekirdek yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="41eb5-201">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="41eb5-202">Bir üretim uygulaması çağırmaz `Database.Migrate` .</span><span class="sxs-lookup"><span data-stu-id="41eb5-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="41eb5-203">Çalıştırılmayan aşağıdaki özel durumu engellemek için önceki koda eklenir `Update-Database` :</span><span class="sxs-lookup"><span data-stu-id="41eb5-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="41eb5-204">SqlException: oturum açma tarafından istenen "RazorPagesMovieContext-21" veritabanı açılamıyor.</span><span class="sxs-lookup"><span data-stu-id="41eb5-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="41eb5-205">Oturum açılamadı.</span><span class="sxs-lookup"><span data-stu-id="41eb5-205">The login failed.</span></span>
<span data-ttu-id="41eb5-206">' Kullanıcı adı ' kullanıcısı için oturum açma başarısız.</span><span class="sxs-lookup"><span data-stu-id="41eb5-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="41eb5-207">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="41eb5-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41eb5-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41eb5-209">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="41eb5-209">Delete all the records in the DB.</span></span> <span data-ttu-id="41eb5-210">Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="41eb5-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="41eb5-211">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (sınıftaki yöntemleri çağırın `Startup` ).</span><span class="sxs-lookup"><span data-stu-id="41eb5-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="41eb5-212">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="41eb5-213">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="41eb5-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="41eb5-214">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:</span><span class="sxs-lookup"><span data-stu-id="41eb5-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="41eb5-217">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="41eb5-218">Ile hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="41eb5-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41eb5-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="41eb5-220">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="41eb5-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="41eb5-221">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="41eb5-222">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41eb5-223">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41eb5-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="41eb5-224">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="41eb5-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="41eb5-225">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="41eb5-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="41eb5-226">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="41eb5-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="41eb5-227">Uygulama, sağlanan verileri gösterir:</span><span class="sxs-lookup"><span data-stu-id="41eb5-227">The app shows the seeded data:</span></span>

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55.png)

<span data-ttu-id="41eb5-229">Sonraki öğretici, verilerin sunumunu temizler.</span><span class="sxs-lookup"><span data-stu-id="41eb5-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41eb5-230">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="41eb5-230">Additional resources</span></span>

* [<span data-ttu-id="41eb5-231">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="41eb5-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="41eb5-232">[Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="41eb5-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
