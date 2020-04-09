---
title: ASP.NET Core MVC uygulamasında SQL ile çalışma
author: rick-anderson
description: ASP.NET Core MVC uygulamasında SQL Server LocalDB veya SQLite'yi kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665041"
---
# <a name="work-with-sql-in-aspnet-core"></a><span data-ttu-id="f8697-103">ASP.NET Core'da SQL ile çalışma</span><span class="sxs-lookup"><span data-stu-id="f8697-103">Work with SQL in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8697-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8697-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8697-105">Nesne, `MvcMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="f8697-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f8697-106">Veritabanı *bağlamı, Startup.cs* dosyasındaki `ConfigureServices` yöntemde [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f8697-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f8697-108">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="f8697-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f8697-109">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="f8697-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-110">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="f8697-111">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="f8697-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f8697-112">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="f8697-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="f8697-113">Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini üretim SQL Server'a ayarlamak için bir ortam değişkeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f8697-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="f8697-114">Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="f8697-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f8697-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f8697-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f8697-117">LocalDB, SQL Server Express Veritabanı Altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="f8697-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="f8697-118">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="f8697-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f8697-119">Varsayılan olarak, LocalDB veritabanı *C:/Users/{user}* dizininde *.mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f8697-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="f8697-120">**Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="f8697-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="f8697-122">**Tasarımcıyı**> `Movie` tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="f8697-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/design.png)

  ![Designer'da film masası açıldı](working-with-sql/_static/dv.png)

<span data-ttu-id="f8697-125">Yanındaki anahtar simgesine `ID`dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f8697-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f8697-126">Varsayılan olarak, EF birincil `ID` anahtar adlı bir özellik yapar.</span><span class="sxs-lookup"><span data-stu-id="f8697-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="f8697-127">Verileri Görüntüleme `Movie` **>** tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="f8697-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açık](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-130">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="f8697-131">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="f8697-131">Seed the database</span></span>

<span data-ttu-id="f8697-132">Modeller klasöründe `SeedData` yeni *Models* bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f8697-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="f8697-133">Oluşturulan kodu aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f8697-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f8697-134">DB'de film varsa, tohum başdöndürür ve film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8697-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f8697-135">Tohum başharfekle</span><span class="sxs-lookup"><span data-stu-id="f8697-135">Add the seed initializer</span></span>

<span data-ttu-id="f8697-136">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f8697-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="f8697-137">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f8697-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8697-139">DB'deki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="f8697-139">Delete all the records in the DB.</span></span> <span data-ttu-id="f8697-140">Bunu tarayıcıdaki silme bağlantılarıyla veya SSOX'tan yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f8697-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="f8697-141">Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar.</span><span class="sxs-lookup"><span data-stu-id="f8697-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f8697-142">Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f8697-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f8697-143">Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f8697-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f8697-144">Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Siteden Çık** veya **Durdur'a** dokunun</span><span class="sxs-lookup"><span data-stu-id="f8697-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="f8697-147">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="f8697-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="f8697-148">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklamayı durdurun ve F5 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="f8697-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-149">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f8697-150">DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="f8697-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f8697-151">Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="f8697-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="f8697-152">Uygulama, tohumlu verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f8697-152">The app shows the seeded data.</span></span>

![Microsoft Edge'de film verilerini gösteren MVC Film uygulaması açıldı](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f8697-154">[Önceki](adding-model.md)
> [Sonraki](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="f8697-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8697-155">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8697-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8697-156">Nesne, `MvcMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler.</span><span class="sxs-lookup"><span data-stu-id="f8697-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="f8697-157">Veritabanı *bağlamı, Startup.cs* dosyasındaki `ConfigureServices` yöntemde [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="f8697-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="f8697-159">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="f8697-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f8697-160">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="f8697-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-161">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f8697-162">ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur.</span><span class="sxs-lookup"><span data-stu-id="f8697-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="f8697-163">Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:</span><span class="sxs-lookup"><span data-stu-id="f8697-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="f8697-164">Uygulamayı bir test veya üretim sunucusuna dağıttığınızda, bağlantı dizesini gerçek bir SQL Server'a ayarlamak için bir ortam değişkeni veya başka bir yaklaşım kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f8697-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="f8697-165">Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.</span><span class="sxs-lookup"><span data-stu-id="f8697-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="f8697-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f8697-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f8697-168">LocalDB, SQL Server Express Veritabanı Altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="f8697-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="f8697-169">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="f8697-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f8697-170">Varsayılan olarak, LocalDB veritabanı *C:/Users/{user}* dizininde *.mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f8697-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="f8697-171">**Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="f8697-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* <span data-ttu-id="f8697-173">**Tasarımcıyı**> `Movie` tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="f8697-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/design.png)

  ![Designer'da film masası açıldı](working-with-sql/_static/dv.png)

<span data-ttu-id="f8697-176">Yanındaki anahtar simgesine `ID`dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f8697-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="f8697-177">Varsayılan olarak, EF birincil `ID` anahtar adlı bir özellik yapar.</span><span class="sxs-lookup"><span data-stu-id="f8697-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="f8697-178">Verileri Görüntüleme `Movie` **>** tabloya sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="f8697-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açık](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-181">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="f8697-182">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="f8697-182">Seed the database</span></span>

<span data-ttu-id="f8697-183">Modeller klasöründe `SeedData` yeni *Models* bir sınıf oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f8697-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="f8697-184">Oluşturulan kodu aşağıdakilerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f8697-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="f8697-185">DB'de film varsa, tohum başdöndürür ve film eklenmez.</span><span class="sxs-lookup"><span data-stu-id="f8697-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="f8697-186">Tohum başharfekle</span><span class="sxs-lookup"><span data-stu-id="f8697-186">Add the seed initializer</span></span>

<span data-ttu-id="f8697-187">*Program.cs* içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="f8697-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="f8697-188">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f8697-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8697-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8697-190">DB'deki tüm kayıtları silin.</span><span class="sxs-lookup"><span data-stu-id="f8697-190">Delete all the records in the DB.</span></span> <span data-ttu-id="f8697-191">Bunu tarayıcıdaki silme bağlantılarıyla veya SSOX'tan yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f8697-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="f8697-192">Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar.</span><span class="sxs-lookup"><span data-stu-id="f8697-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="f8697-193">Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f8697-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="f8697-194">Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f8697-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="f8697-195">Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Siteden Çık** veya **Durdur'a** dokunun</span><span class="sxs-lookup"><span data-stu-id="f8697-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="f8697-198">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="f8697-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="f8697-199">Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklamayı durdurun ve F5 tuşuna basın</span><span class="sxs-lookup"><span data-stu-id="f8697-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f8697-200">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8697-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f8697-201">DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır).</span><span class="sxs-lookup"><span data-stu-id="f8697-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="f8697-202">Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.</span><span class="sxs-lookup"><span data-stu-id="f8697-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="f8697-203">Uygulama, tohumlu verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f8697-203">The app shows the seeded data.</span></span>

![Microsoft Edge'de film verilerini gösteren MVC Film uygulaması açıldı](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="f8697-205">[Önceki](adding-model.md)
> [Sonraki](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="f8697-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
