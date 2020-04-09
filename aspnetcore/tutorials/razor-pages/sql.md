---
title: Bir veritabanı ve ASP.NET Core ile çalışın
author: rick-anderson
description: Bir veritabanı ve ASP.NET Core ile çalışmayı açıklar.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664341"
---
# <a name="work-with-a-database-and-aspnet-core"></a><span data-ttu-id="d80d8-103">Bir veritabanı ve ASP.NET Core ile çalışın</span><span class="sxs-lookup"><span data-stu-id="d80d8-103">Work with a database and ASP.NET Core</span></span>

<span data-ttu-id="d80d8-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d80d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d80d8-105">Nesne, `RazorPagesMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="d80d8-105">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d80d8-106">Veritabanı *bağlamı, Startup.cs*yönteminde `ConfigureServices` [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="d80d8-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-108">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="d80d8-109">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-109">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d80d8-110">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-110">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d80d8-112">Veritabanının ad değeri`Database={Database name}`( ) oluşturduğunuz kod için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-112">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="d80d8-113">Ad değeri rasgele.</span><span class="sxs-lookup"><span data-stu-id="d80d8-113">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-114">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="d80d8-115">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="d80d8-116">Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-116">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d80d8-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d80d8-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d80d8-119">LocalDB, SQL Server Express veritabanı altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="d80d8-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="d80d8-120">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d80d8-121">Varsayılan olarak, LocalDB `*.mdf` veritabanı dizinde `C:\Users\<user>\` dosyalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="d80d8-122">**Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="d80d8-124">Tabloya sağ `Movie` tıklayın ve **Tasarımcıyı Görüntüle'yi**seçin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-124">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Film tablosunda bağlamsal menüler açılır](sql/_static/design.png)

  ![Designer'da film masaları açıldı](sql/_static/dv.png)

<span data-ttu-id="d80d8-127">Yanındaki anahtar simgesine `ID`dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d80d8-128">Varsayılan olarak, EF birincil `ID` anahtar için adlandırılmış bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-128">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="d80d8-129">Tabloya sağ `Movie` tıklayın ve **Verileri Görüntüle'yi**seçin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-129">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açık](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-131">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="d80d8-132">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="d80d8-132">Seed the database</span></span>

<span data-ttu-id="d80d8-133">Modeller klasöründe `SeedData` aşağıdaki *Models* kodla adında yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d80d8-133">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d80d8-134">DB'de film varsa, tohum başdöndürür ve film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="d80d8-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d80d8-135">Tohum başharfekle</span><span class="sxs-lookup"><span data-stu-id="d80d8-135">Add the seed initializer</span></span>

<span data-ttu-id="d80d8-136">*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-136">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="d80d8-137">Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-137">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="d80d8-138">Tohum yöntemini çağırın, ona bağlamı geçirin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-138">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="d80d8-139">Tohum yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-139">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="d80d8-140">Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-140">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="d80d8-141">Aşağıdaki özel durum `Update-Database` çalıştırıldığında oluşur:</span><span class="sxs-lookup"><span data-stu-id="d80d8-141">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="d80d8-142">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="d80d8-142">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-143">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d80d8-144">DB'deki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-144">Delete all the records in the DB.</span></span> <span data-ttu-id="d80d8-145">Bunu tarayıcıdaki silme bağlantılarıyla veya [SSOX'tan](xref:tutorials/razor-pages/new-field#ssox) yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="d80d8-145">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="d80d8-146">Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar.</span><span class="sxs-lookup"><span data-stu-id="d80d8-146">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d80d8-147">Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-147">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d80d8-148">Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d80d8-148">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d80d8-149">Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **Durdur Sitesi'ne**dokunun:</span><span class="sxs-lookup"><span data-stu-id="d80d8-149">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="d80d8-152">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-152">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="d80d8-153">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-153">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-154">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-154">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d80d8-155">DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="d80d8-155">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d80d8-156">Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-156">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d80d8-157">Uygulama, tohumlu verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-157">The app shows the seeded data.</span></span>

---

<span data-ttu-id="d80d8-158">Bir sonraki öğretici, verilerin sunumuna iyi gelecektir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-158">The next tutorial will improve the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d80d8-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d80d8-159">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d80d8-160">[Önceki: İskeleli Jilet Sayfaları](xref:tutorials/razor-pages/page)
> [Sonraki: Sayfaları Güncelleme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="d80d8-160">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d80d8-161">Nesne, `RazorPagesMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="d80d8-161">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="d80d8-162">Veritabanı *bağlamı, Startup.cs*yönteminde `ConfigureServices` [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="d80d8-162">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-164">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="d80d8-165">Kullanılan `ConfigureServices`yöntemler hakkında daha fazla bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="d80d8-165">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="d80d8-166">[AB Genel Veri Koruma Yönetmeliği (GDPR) için ASP.NET Çekirdek](xref:security/gdpr) `CookiePolicyOptions`desteği.</span><span class="sxs-lookup"><span data-stu-id="d80d8-166">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="d80d8-167">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="d80d8-167">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="d80d8-168">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-168">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="d80d8-169">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-169">For local development, it gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d80d8-171">Veritabanının ad değeri`Database={Database name}`( ) oluşturduğunuz kod için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-171">The name value for the database (`Database={Database name}`) will be different for your generated code.</span></span> <span data-ttu-id="d80d8-172">Ad değeri rasgele.</span><span class="sxs-lookup"><span data-stu-id="d80d8-172">The name value is arbitrary.</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d80d8-173">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d80d8-173">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-174">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="d80d8-175">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-175">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a real database server.</span></span> <span data-ttu-id="d80d8-176">Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-176">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="d80d8-178">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="d80d8-178">SQL Server Express LocalDB</span></span>

<span data-ttu-id="d80d8-179">LocalDB, SQL Server Express veritabanı altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="d80d8-179">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="d80d8-180">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-180">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="d80d8-181">Varsayılan olarak, LocalDB `*.mdf` veritabanı dizinde `C:/Users/<user/>` dosyalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-181">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="d80d8-182">**Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-182">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](sql/_static/ssox.png)

* <span data-ttu-id="d80d8-184">Tabloya sağ `Movie` tıklayın ve **Tasarımcıyı Görüntüle'yi**seçin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-184">Right click on the `Movie` table and select **View Designer**:</span></span>

  ![Film masasında bağlamsal menü açılır](sql/_static/design.png)

  ![Designer'da film masası açıldı](sql/_static/dv.png)

<span data-ttu-id="d80d8-187">Yanındaki anahtar simgesine `ID`dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-187">Note the key icon next to `ID`.</span></span> <span data-ttu-id="d80d8-188">Varsayılan olarak, EF birincil `ID` anahtar için adlandırılmış bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d80d8-188">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="d80d8-189">Tabloya sağ `Movie` tıklayın ve **Verileri Görüntüle'yi**seçin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-189">Right click on the `Movie` table and select **View Data**:</span></span>

  ![Tablo verilerini gösteren film tablosu açık](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d80d8-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d80d8-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-192">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-192">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="d80d8-193">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="d80d8-193">Seed the database</span></span>

<span data-ttu-id="d80d8-194">Modeller klasöründe `SeedData` aşağıdaki *Models* kodla adında yeni bir sınıf oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d80d8-194">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="d80d8-195">DB'de film varsa, tohum başdöndürür ve film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="d80d8-195">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="d80d8-196">Tohum başharfekle</span><span class="sxs-lookup"><span data-stu-id="d80d8-196">Add the seed initializer</span></span>

<span data-ttu-id="d80d8-197">*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="d80d8-197">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="d80d8-198">Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-198">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="d80d8-199">Tohum yöntemini çağırın, ona bağlamı geçirin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-199">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="d80d8-200">Tohum yöntemi tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-200">Dispose the context when the seed method completes.</span></span>

<span data-ttu-id="d80d8-201">Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-201">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="d80d8-202">Bir üretim uygulaması `Database.Migrate`aramaz.</span><span class="sxs-lookup"><span data-stu-id="d80d8-202">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="d80d8-203">Çalıştırıldığında aşağıdaki özel durumu `Update-Database` önlemek için önceki koda eklenir:</span><span class="sxs-lookup"><span data-stu-id="d80d8-203">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="d80d8-204">SqlException: Giriş tarafından istenen "RazorPagesMovieContext-21" veritabanını açılamaz.</span><span class="sxs-lookup"><span data-stu-id="d80d8-204">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="d80d8-205">Giriş başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="d80d8-205">The login failed.</span></span>
<span data-ttu-id="d80d8-206">Giriş kullanıcı 'kullanıcı adı' için başarısız oldu.</span><span class="sxs-lookup"><span data-stu-id="d80d8-206">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="d80d8-207">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="d80d8-207">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d80d8-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d80d8-209">DB'deki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="d80d8-209">Delete all the records in the DB.</span></span> <span data-ttu-id="d80d8-210">Bunu tarayıcıdaki silme bağlantılarıyla veya [SSOX'tan](xref:tutorials/razor-pages/new-field#ssox) yapabilirsiniz</span><span class="sxs-lookup"><span data-stu-id="d80d8-210">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="d80d8-211">Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar.</span><span class="sxs-lookup"><span data-stu-id="d80d8-211">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="d80d8-212">Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d80d8-212">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="d80d8-213">Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d80d8-213">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="d80d8-214">Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **Durdur Sitesi'ne**dokunun:</span><span class="sxs-lookup"><span data-stu-id="d80d8-214">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * <span data-ttu-id="d80d8-217">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-217">If you were running VS in non-debug mode, press F5 to run in debug mode.</span></span>
    * <span data-ttu-id="d80d8-218">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-218">If you were running VS in debug mode, stop the debugger and press F5.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d80d8-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d80d8-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d80d8-220">DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="d80d8-220">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d80d8-221">Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-221">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d80d8-222">Uygulama, tohumlu verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-222">The app shows the seeded data.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d80d8-223">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d80d8-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d80d8-224">DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="d80d8-224">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="d80d8-225">Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="d80d8-225">Stop and start the app to seed the database.</span></span>

<span data-ttu-id="d80d8-226">Uygulama, tohumlu verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-226">The app shows the seeded data.</span></span>

---

<span data-ttu-id="d80d8-227">Uygulama tohumlu verileri gösterir:</span><span class="sxs-lookup"><span data-stu-id="d80d8-227">The app shows the seeded data:</span></span>

![Chrome'da film verilerini gösteren film uygulaması açıldı](sql/_static/m55.png)

<span data-ttu-id="d80d8-229">Bir sonraki öğretici, verilerin sunumuna temizlenir.</span><span class="sxs-lookup"><span data-stu-id="d80d8-229">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d80d8-230">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d80d8-230">Additional resources</span></span>

* [<span data-ttu-id="d80d8-231">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="d80d8-231">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="d80d8-232">[Önceki: İskeleli Jilet Sayfaları](xref:tutorials/razor-pages/page)
> [Sonraki: Sayfaları Güncelleme](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="d80d8-232">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Updating the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
