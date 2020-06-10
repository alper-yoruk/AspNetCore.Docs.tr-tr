---
title: 5. bölüm, ASP.NET Core MVC uygulamasındaki bir veritabanıyla çalışma
author: rick-anderson
description: 5. bölüm, ASP.NET Core MVC uygulamasına model ekleme
ms.author: riande
ms.date: 8/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: 7fdd147f4de00a593a77977d2b53e5369667fe78
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652515"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="4d8ab-103">5. bölüm, ASP.NET Core MVC uygulamasındaki bir veritabanıyla çalışma</span><span class="sxs-lookup"><span data-stu-id="4d8ab-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4d8ab-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d8ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d8ab-105">`MvcMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="4d8ab-106">Veritabanı bağlamı, Startup.cs dosyasındaki yönteminde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4d8ab-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="4d8ab-108">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="4d8ab-109">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-110">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="4d8ab-111">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="4d8ab-112">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="4d8ab-113">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir üretim SQL Server ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="4d8ab-114">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="4d8ab-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="4d8ab-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="4d8ab-117">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="4d8ab-118">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="4d8ab-119">Varsayılan olarak, LocalDB veritabanı *C:/Users/{User}* dizininde *. mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="4d8ab-120">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="4d8ab-122">`Movie`Tablo **> görünüm tasarımcısına** sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](working-with-sql/_static/dv.png)

<span data-ttu-id="4d8ab-125">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="4d8ab-126">Varsayılan olarak, EF birincil anahtar adlı bir özellik oluşturacak `ID` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="4d8ab-127">`Movie` **Verileri görüntülemek>** tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açma](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-130">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="4d8ab-131">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="4d8ab-131">Seed the database</span></span>

<span data-ttu-id="4d8ab-132">Modeller klasöründe adlı yeni bir sınıf oluşturun `SeedData` . *Models*</span><span class="sxs-lookup"><span data-stu-id="4d8ab-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="4d8ab-133">Oluşturulan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="4d8ab-134">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="4d8ab-135">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="4d8ab-135">Add the seed initializer</span></span>

<span data-ttu-id="4d8ab-136">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="4d8ab-137">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="4d8ab-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d8ab-139">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-139">Delete all the records in the DB.</span></span> <span data-ttu-id="4d8ab-140">Bunu, tarayıcıda veya SSOX 'ten silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="4d8ab-141">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (sınıftaki yöntemleri çağırın `Startup` ).</span><span class="sxs-lookup"><span data-stu-id="4d8ab-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="4d8ab-142">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="4d8ab-143">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="4d8ab-144">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur** ' a dokunun</span><span class="sxs-lookup"><span data-stu-id="4d8ab-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="4d8ab-147">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 'e basın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="4d8ab-148">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-149">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4d8ab-150">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="4d8ab-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="4d8ab-151">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="4d8ab-152">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-152">The app shows the seeded data.</span></span>

![Microsoft Edge 'de film verilerini gösteren MVC film uygulaması açık](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="4d8ab-154">[Önceki](adding-model.md) 
>  [Sonraki](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="4d8ab-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d8ab-155">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d8ab-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d8ab-156">`MvcMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="4d8ab-157">Veritabanı bağlamı, Startup.cs dosyasındaki yönteminde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4d8ab-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="4d8ab-159">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="4d8ab-160">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-161">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="4d8ab-162">ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi okur `ConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="4d8ab-163">Yerel geliştirme için, *appSettings. JSON* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="4d8ab-164">Uygulamayı bir test veya üretim sunucusuna dağıtırken, bağlantı dizesini gerçek bir SQL Server ayarlamak için bir ortam değişkeni veya başka bir yaklaşım kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="4d8ab-165">Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="4d8ab-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="4d8ab-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="4d8ab-168">LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="4d8ab-169">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="4d8ab-170">Varsayılan olarak, LocalDB veritabanı *C:/Users/{User}* dizininde *. mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="4d8ab-171">**Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="4d8ab-173">`Movie`Tablo **> görünüm tasarımcısına** sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](working-with-sql/_static/dv.png)

<span data-ttu-id="4d8ab-176">Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="4d8ab-177">Varsayılan olarak, EF birincil anahtar adlı bir özellik oluşturacak `ID` .</span><span class="sxs-lookup"><span data-stu-id="4d8ab-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="4d8ab-178">`Movie` **Verileri görüntülemek>** tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Film tablosunda bağlam menüsü açık](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açma](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-181">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="4d8ab-182">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="4d8ab-182">Seed the database</span></span>

<span data-ttu-id="4d8ab-183">Modeller klasöründe adlı yeni bir sınıf oluşturun `SeedData` . *Models*</span><span class="sxs-lookup"><span data-stu-id="4d8ab-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="4d8ab-184">Oluşturulan kodu aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="4d8ab-185">VERITABANıNDA herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="4d8ab-186">Tohum başlatıcısı ekleme</span><span class="sxs-lookup"><span data-stu-id="4d8ab-186">Add the seed initializer</span></span>

<span data-ttu-id="4d8ab-187">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="4d8ab-188">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="4d8ab-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d8ab-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4d8ab-190">VERITABANıNDAKI tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-190">Delete all the records in the DB.</span></span> <span data-ttu-id="4d8ab-191">Bunu, tarayıcıda veya SSOX 'ten silme bağlantılarıyla yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="4d8ab-192">Çekirdek yöntemin çalışması için uygulamayı başlamaya zorlayın (sınıftaki yöntemleri çağırın `Startup` ).</span><span class="sxs-lookup"><span data-stu-id="4d8ab-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="4d8ab-193">Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="4d8ab-194">Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="4d8ab-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="4d8ab-195">Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur** ' a dokunun</span><span class="sxs-lookup"><span data-stu-id="4d8ab-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="4d8ab-198">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 'e basın</span><span class="sxs-lookup"><span data-stu-id="4d8ab-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="4d8ab-199">VS hata ayıklama modunda çalıştırıyorsanız, hata ayıklayıcıyı durdurun ve F5 tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d8ab-200">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d8ab-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4d8ab-201">VERITABANıNDAKI tüm kayıtları silin (Bu nedenle çekirdek yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="4d8ab-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="4d8ab-202">Veritabanını temel alarak uygulamayı durdurup başlatın.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="4d8ab-203">Uygulama, sağlanan verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="4d8ab-203">The app shows the seeded data.</span></span>

![Microsoft Edge 'de film verilerini gösteren MVC film uygulaması açık](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="4d8ab-205">[Önceki](adding-model.md) 
>  [Sonraki](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="4d8ab-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
