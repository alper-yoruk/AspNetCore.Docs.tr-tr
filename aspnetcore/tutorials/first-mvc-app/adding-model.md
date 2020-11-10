---
title: 4. bölüm, ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: ASP.NET Core MVC 'deki öğretici serisinin 4. bölümü.
ms.author: riande
ms.date: 01/13/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422742"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="3cc87-103">4. bölüm, ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="3cc87-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="3cc87-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3cc87-105">Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="3cc87-106">Bu sınıflar, **d** VC uygulamasının " **d** odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="3cc87-107">Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="3cc87-108">EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="3cc87-109">Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P** Lain **C** **O** ) **O**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3cc87-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="3cc87-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="3cc87-111">Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="3cc87-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-114">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="3cc87-115">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3cc87-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cc87-119">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="3cc87-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="3cc87-120">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="3cc87-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="3cc87-122">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="3cc87-123"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3cc87-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3cc87-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="3cc87-124">With this attribute:</span></span>

* <span data-ttu-id="3cc87-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="3cc87-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3cc87-127">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="3cc87-128">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-130">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3cc87-132">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="3cc87-133">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="3cc87-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="3cc87-134">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="3cc87-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="3cc87-135">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cc87-138">**Proje** menüsünde, **NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="3cc87-139">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="3cc87-140">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="3cc87-142">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="3cc87-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-143">Press the **Ok** button.</span></span>

<span data-ttu-id="3cc87-144">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="3cc87-145">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="3cc87-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="3cc87-147">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3cc87-147">Create a database context class</span></span>

<span data-ttu-id="3cc87-148">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="3cc87-149">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="3cc87-150">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3cc87-150">Create a *Data* folder.</span></span>

<span data-ttu-id="3cc87-151">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="3cc87-152">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3cc87-153">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3cc87-154">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="3cc87-155">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="3cc87-155">Register the database context</span></span>

<span data-ttu-id="3cc87-156">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3cc87-157">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="3cc87-158">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3cc87-159">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="3cc87-160">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="3cc87-161">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3cc87-162">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-164">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="3cc87-165">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3cc87-166">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="3cc87-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="3cc87-167">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="3cc87-167">Add a database connection string</span></span>

<span data-ttu-id="3cc87-168">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="3cc87-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-170">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="3cc87-171">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="3cc87-172">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="3cc87-172">Scaffold movie pages</span></span>

<span data-ttu-id="3cc87-173">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-175">**Çözüm Gezgini** , *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="3cc87-177">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="3cc87-179">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="3cc87-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="3cc87-180">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="3cc87-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="3cc87-181">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="3cc87-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc5.png)

* <span data-ttu-id="3cc87-183">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="3cc87-184">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="3cc87-185">**Ekle** ’yi seçin</span><span class="sxs-lookup"><span data-stu-id="3cc87-185">Select **Add**</span></span>

<span data-ttu-id="3cc87-186">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3cc87-186">Visual Studio creates:</span></span>

* <span data-ttu-id="3cc87-187">Bir filmler denetleyicisi ( *denetleyiciler/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="3cc87-188">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme ( *Görünümler/filmler/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="3cc87-189">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="3cc87-191">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="3cc87-192">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="3cc87-193">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-194">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3cc87-195">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="3cc87-196">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="3cc87-197">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="3cc87-198">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="3cc87-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="3cc87-199">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="3cc87-199">Initial migration</span></span>

<span data-ttu-id="3cc87-200">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="3cc87-201">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-203">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="3cc87-204">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="3cc87-205">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="3cc87-206">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3cc87-207">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="3cc87-208">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="3cc87-209">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="3cc87-210">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="3cc87-211">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="3cc87-212">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="3cc87-213">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="3cc87-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3cc87-214">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3cc87-215">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="3cc87-216">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-217">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3cc87-218">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="3cc87-219">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="3cc87-220">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3cc87-221">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="3cc87-222">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="3cc87-223">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="3cc87-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="3cc87-224">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="3cc87-225">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="3cc87-226">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="3cc87-226">The InitialCreate class</span></span>

<span data-ttu-id="3cc87-227">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="3cc87-228">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="3cc87-229">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="3cc87-230">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="3cc87-230">Test the app</span></span>

* <span data-ttu-id="3cc87-231">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="3cc87-232">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="3cc87-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-234">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="3cc87-235">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="3cc87-236">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-236">Test the **Create** page.</span></span> <span data-ttu-id="3cc87-237">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="3cc87-238">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3cc87-239">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3cc87-240">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3cc87-241">**Düzenleme** , **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="3cc87-242">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-244">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3cc87-245">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3cc87-246">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-247">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3cc87-248">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3cc87-249">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3cc87-250">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="3cc87-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3cc87-251">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3cc87-252">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="3cc87-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3cc87-253">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="3cc87-254">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="3cc87-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="3cc87-255">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="3cc87-256">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="3cc87-257">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="3cc87-258">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="3cc87-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="3cc87-259">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="3cc87-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="3cc87-260">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="3cc87-261">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="3cc87-262">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="3cc87-263">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="3cc87-264">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="3cc87-265">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="3cc87-266">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3cc87-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="3cc87-267">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="3cc87-268">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="3cc87-269">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="3cc87-270">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="3cc87-271">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="3cc87-272">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="3cc87-273">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="3cc87-274">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="3cc87-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="3cc87-275">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="3cc87-276">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="3cc87-277">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="3cc87-278">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="3cc87-279">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="3cc87-280">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="3cc87-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="3cc87-281">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3cc87-282">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="3cc87-283">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="3cc87-284">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cc87-285">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3cc87-285">Additional resources</span></span>

* [<span data-ttu-id="3cc87-286">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="3cc87-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="3cc87-287">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="3cc87-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="3cc87-288">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="3cc87-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="3cc87-289">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-291">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="3cc87-292">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3cc87-294">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-295">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cc87-296">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="3cc87-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="3cc87-297">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="3cc87-298">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="3cc87-299">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="3cc87-300"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="3cc87-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="3cc87-301">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="3cc87-301">With this attribute:</span></span>

* <span data-ttu-id="3cc87-302">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="3cc87-303">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="3cc87-304">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="3cc87-305">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-307">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3cc87-309">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="3cc87-310">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="3cc87-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="3cc87-311">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="3cc87-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="3cc87-312">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-314">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3cc87-315">**Proje** menüsünde, **NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="3cc87-316">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="3cc87-317">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="3cc87-319">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="3cc87-320">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-320">Press the **Ok** button.</span></span>

<span data-ttu-id="3cc87-321">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="3cc87-322">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="3cc87-323">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="3cc87-324">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="3cc87-324">Create a database context class</span></span>

<span data-ttu-id="3cc87-325">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="3cc87-326">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="3cc87-327">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3cc87-327">Create a *Data* folder.</span></span>

<span data-ttu-id="3cc87-328">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="3cc87-329">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3cc87-330">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3cc87-331">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="3cc87-332">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="3cc87-332">Register the database context</span></span>

<span data-ttu-id="3cc87-333">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3cc87-334">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="3cc87-335">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3cc87-336">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="3cc87-337">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="3cc87-338">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="3cc87-339">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-341">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="3cc87-342">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3cc87-343">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="3cc87-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="3cc87-344">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="3cc87-344">Add a database connection string</span></span>

<span data-ttu-id="3cc87-345">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="3cc87-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-347">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="3cc87-348">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="3cc87-349">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="3cc87-349">Scaffold movie pages</span></span>

<span data-ttu-id="3cc87-350">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-352">**Çözüm Gezgini** , *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="3cc87-354">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="3cc87-356">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="3cc87-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="3cc87-357">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="3cc87-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="3cc87-358">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="3cc87-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* <span data-ttu-id="3cc87-360">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="3cc87-361">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="3cc87-362">**Ekle** ’yi seçin</span><span class="sxs-lookup"><span data-stu-id="3cc87-362">Select **Add**</span></span>

<span data-ttu-id="3cc87-363">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3cc87-363">Visual Studio creates:</span></span>

* <span data-ttu-id="3cc87-364">Bir filmler denetleyicisi ( *denetleyiciler/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="3cc87-365">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme ( *Görünümler/filmler/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="3cc87-366">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="3cc87-368">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="3cc87-369">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="3cc87-370">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-371">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3cc87-372">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="3cc87-373">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="3cc87-374">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="3cc87-375">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="3cc87-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="3cc87-376">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="3cc87-376">Initial migration</span></span>

<span data-ttu-id="3cc87-377">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="3cc87-378">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-380">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="3cc87-381">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="3cc87-382">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="3cc87-383">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3cc87-384">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="3cc87-385">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="3cc87-386">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="3cc87-387">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="3cc87-388">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="3cc87-389">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="3cc87-390">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="3cc87-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3cc87-391">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3cc87-392">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="3cc87-393">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-394">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="3cc87-395">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="3cc87-396">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="3cc87-397">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3cc87-398">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="3cc87-399">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="3cc87-400">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="3cc87-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="3cc87-401">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="3cc87-402">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="3cc87-403">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="3cc87-403">The InitialCreate class</span></span>

<span data-ttu-id="3cc87-404">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="3cc87-405">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="3cc87-406">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="3cc87-407">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="3cc87-407">Test the app</span></span>

* <span data-ttu-id="3cc87-408">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="3cc87-409">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="3cc87-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-411">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="3cc87-412">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="3cc87-413">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-413">Test the **Create** page.</span></span> <span data-ttu-id="3cc87-414">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="3cc87-415">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3cc87-416">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3cc87-417">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3cc87-418">**Düzenleme** , **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="3cc87-419">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-421">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3cc87-422">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3cc87-423">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-424">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3cc87-425">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3cc87-426">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="3cc87-427">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="3cc87-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="3cc87-428">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="3cc87-429">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="3cc87-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="3cc87-430">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="3cc87-431">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="3cc87-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="3cc87-432">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="3cc87-433">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="3cc87-434">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="3cc87-435">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="3cc87-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="3cc87-436">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="3cc87-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="3cc87-437">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="3cc87-438">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="3cc87-439">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="3cc87-440">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="3cc87-441">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="3cc87-442">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="3cc87-443">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3cc87-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="3cc87-444">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="3cc87-445">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="3cc87-446">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="3cc87-447">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="3cc87-448">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="3cc87-449">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="3cc87-450">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="3cc87-451">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="3cc87-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="3cc87-452">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="3cc87-453">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="3cc87-454">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="3cc87-455">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="3cc87-456">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="3cc87-457">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="3cc87-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="3cc87-458">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3cc87-459">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="3cc87-460">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="3cc87-461">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cc87-462">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3cc87-462">Additional resources</span></span>

* [<span data-ttu-id="3cc87-463">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="3cc87-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="3cc87-464">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="3cc87-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="3cc87-465">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="3cc87-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="3cc87-466">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="3cc87-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-468">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="3cc87-469">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-470">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3cc87-471">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3cc87-472">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="3cc87-472">Scaffold the movie model</span></span>

<span data-ttu-id="3cc87-473">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3cc87-474">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-476">**Çözüm Gezgini** , *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="3cc87-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="3cc87-478">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="3cc87-480">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="3cc87-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="3cc87-481">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="3cc87-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="3cc87-482">**Veri bağlamı sınıfı:** Simgeyi seçin **+** ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin</span><span class="sxs-lookup"><span data-stu-id="3cc87-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* <span data-ttu-id="3cc87-484">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="3cc87-485">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="3cc87-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="3cc87-486">**Ekle** ’yi seçin</span><span class="sxs-lookup"><span data-stu-id="3cc87-486">Select **Add**</span></span>

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="3cc87-488">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="3cc87-488">Visual Studio creates:</span></span>

* <span data-ttu-id="3cc87-489">Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext. cs* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="3cc87-490">Bir filmler denetleyicisi ( *denetleyiciler/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="3cc87-491">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme ( *Görünümler/filmler/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="3cc87-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="3cc87-492">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3cc87-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cc87-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3cc87-494">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="3cc87-495">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="3cc87-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3cc87-496">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="3cc87-497">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-498">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3cc87-499">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="3cc87-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="3cc87-500">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="3cc87-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3cc87-501">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="3cc87-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="3cc87-502">Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="3cc87-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-504">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="3cc87-505">Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="3cc87-506">Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3cc87-507">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="3cc87-507">Initial migration</span></span>

<span data-ttu-id="3cc87-508">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="3cc87-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="3cc87-509">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-509">Add an initial migration.</span></span>
* <span data-ttu-id="3cc87-510">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3cc87-512">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="3cc87-514">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="3cc87-515">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="3cc87-516">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="3cc87-517">`Initial`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="3cc87-518">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="3cc87-519">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="3cc87-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="3cc87-520">`Update-Database`Komutu, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-521">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="3cc87-522">`ef migrations add InitialCreate`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="3cc87-523">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="3cc87-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="3cc87-524">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="3cc87-525">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3cc87-526">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="3cc87-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3cc87-527">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3cc87-528">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="3cc87-529">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3cc87-530">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc87-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cc87-532">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.</span><span class="sxs-lookup"><span data-stu-id="3cc87-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="3cc87-533">Aşağıdaki yöntemi inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3cc87-534">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="3cc87-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="3cc87-535">`MvcMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="3cc87-536">Veri bağlamı ( `MvcMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3cc87-537">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="3cc87-538">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3cc87-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3cc87-539">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3cc87-540">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3cc87-541">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3cc87-542">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="3cc87-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3cc87-543">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc87-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3cc87-544">Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3cc87-545">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="3cc87-545">Test the app</span></span>

* <span data-ttu-id="3cc87-546">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3cc87-547">Aşağıdakine benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="3cc87-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3cc87-548">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="3cc87-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3cc87-549">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-549">Test the **Create** link.</span></span> <span data-ttu-id="3cc87-550">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="3cc87-551">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3cc87-552">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3cc87-553">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="3cc87-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3cc87-554">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="3cc87-555">Sınıfı inceleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="3cc87-556">Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="3cc87-557">`services.AddDbContext<MvcMovieContext>(options =>` kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="3cc87-558">`=>` bir [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="3cc87-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="3cc87-559">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="3cc87-560">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="3cc87-561">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="3cc87-562">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="3cc87-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="3cc87-563">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="3cc87-564">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="3cc87-565">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3cc87-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="3cc87-566">Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="3cc87-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="3cc87-567">Yapı iskelesi mekanizması, bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek), `MoviesController` Yöntem ve görünümleri oluştururken sınıfı ve görünümleri ile kullandı.</span><span class="sxs-lookup"><span data-stu-id="3cc87-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="3cc87-568">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="3cc87-569">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="3cc87-570">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="3cc87-571">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="3cc87-572">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="3cc87-573">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="3cc87-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="3cc87-574">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="3cc87-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="3cc87-575">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="3cc87-576">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="3cc87-577">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="3cc87-578">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="3cc87-579">`@model`Görünüm dosyasının üst kısmına bir ifade ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3cc87-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="3cc87-580">Film denetleyicisini oluştururken, `@model` *Ayrıntılar. cshtml* dosyasının en üstüne aşağıdaki ifade otomatik olarak eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="3cc87-581">Bu `@model` yönerge, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3cc87-582">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="3cc87-583">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="3cc87-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="3cc87-584">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="3cc87-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="3cc87-585">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="3cc87-586">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="3cc87-587">Film denetleyicisini oluştururken, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi otomatik olarak dahil edin:</span><span class="sxs-lookup"><span data-stu-id="3cc87-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="3cc87-588">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="3cc87-589">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="3cc87-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="3cc87-590">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="3cc87-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="3cc87-591">Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="3cc87-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3cc87-592">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3cc87-592">Additional resources</span></span>

* [<span data-ttu-id="3cc87-593">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="3cc87-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="3cc87-594">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="3cc87-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="3cc87-595">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Bir sonraki veritabanıyla çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="3cc87-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
