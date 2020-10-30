---
title: 4. bölüm, ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: ASP.NET Core MVC 'deki öğretici serisinin 4. bölümü.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 428d153cd94c882db16484a3009c86d1f9593538
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050907"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="40af1-103">4. bölüm, ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="40af1-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="40af1-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="40af1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="40af1-105">Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="40af1-106">Bu sınıflar, **d** VC uygulamasının " **d** odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="40af1-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="40af1-107">Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="40af1-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="40af1-108">EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="40af1-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="40af1-109">Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P** Lain **C** **O** ) **O** .</span><span class="sxs-lookup"><span data-stu-id="40af1-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="40af1-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="40af1-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="40af1-111">Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40af1-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="40af1-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="40af1-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-114">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="40af1-114">Right-click the *Models* folder > **Add** > **Class** .</span></span> <span data-ttu-id="40af1-115">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="40af1-115">Name the file *Movie.cs* .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="40af1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40af1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="40af1-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40af1-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="40af1-119">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="40af1-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class** .</span></span> <span data-ttu-id="40af1-120">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="40af1-120">Name the file *Movie.cs* .</span></span>

---

<span data-ttu-id="40af1-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="40af1-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="40af1-122">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="40af1-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="40af1-123"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="40af1-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="40af1-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="40af1-124">With this attribute:</span></span>

* <span data-ttu-id="40af1-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="40af1-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="40af1-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40af1-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="40af1-127">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="40af1-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="40af1-128">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="40af1-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-130">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="40af1-132">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="40af1-133">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="40af1-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="40af1-134">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="40af1-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="40af1-135">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="40af1-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="40af1-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40af1-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40af1-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="40af1-138">**Proje** menüsünde, **NuGet Paketlerini Yönet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-138">From the **Project** menu, select **Manage NuGet Packages** .</span></span>

<span data-ttu-id="40af1-139">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="40af1-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="40af1-140">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="40af1-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="40af1-142">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40af1-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="40af1-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="40af1-143">Press the **Ok** button.</span></span>

<span data-ttu-id="40af1-144">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="40af1-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="40af1-145">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="40af1-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="40af1-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="40af1-147">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="40af1-147">Create a database context class</span></span>

<span data-ttu-id="40af1-148">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="40af1-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="40af1-149">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="40af1-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="40af1-150">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="40af1-150">Create a *Data* folder.</span></span>

<span data-ttu-id="40af1-151">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="40af1-152">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40af1-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="40af1-153">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="40af1-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="40af1-154">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="40af1-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="40af1-155">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="40af1-155">Register the database context</span></span>

<span data-ttu-id="40af1-156">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="40af1-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40af1-157">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="40af1-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="40af1-158">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="40af1-158">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="40af1-159">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="40af1-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="40af1-160">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="40af1-161">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="40af1-162">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="40af1-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-164">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="40af1-165">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="40af1-166">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="40af1-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="40af1-167">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="40af1-167">Add a database connection string</span></span>

<span data-ttu-id="40af1-168">Dosyaya bir bağlantı dizesi ekleyin *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="40af1-168">Add a connection string to the *:::no-loc(appsettings.json):::* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/:::no-loc(appsettings.json):::?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-170">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="40af1-171">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="40af1-172">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="40af1-172">Scaffold movie pages</span></span>

<span data-ttu-id="40af1-173">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="40af1-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-175">**Çözüm Gezgini** , *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin** .</span><span class="sxs-lookup"><span data-stu-id="40af1-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item** .</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="40af1-177">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add** .</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="40af1-179">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="40af1-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="40af1-180">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="40af1-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="40af1-181">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="40af1-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* <span data-ttu-id="40af1-183">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="40af1-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="40af1-184">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="40af1-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="40af1-185">**Ekle** ’yi seçin</span><span class="sxs-lookup"><span data-stu-id="40af1-185">Select **Add**</span></span>

<span data-ttu-id="40af1-186">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="40af1-186">Visual Studio creates:</span></span>

* <span data-ttu-id="40af1-187">Bir filmler denetleyicisi ( *denetleyiciler/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="40af1-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="40af1-188">:::no-loc(Razor)::: Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme ( *Görünümler/filmler/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="40af1-188">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="40af1-189">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="40af1-189">The automatic creation of these files is known as *scaffolding* .</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="40af1-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40af1-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="40af1-191">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="40af1-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="40af1-192">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="40af1-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="40af1-193">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40af1-194">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="40af1-195">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="40af1-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="40af1-196">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="40af1-197">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="40af1-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="40af1-198">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="40af1-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="40af1-199">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="40af1-199">Initial migration</span></span>

<span data-ttu-id="40af1-200">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="40af1-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="40af1-201">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="40af1-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-203">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="40af1-204">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="40af1-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="40af1-205">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40af1-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="40af1-206">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="40af1-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="40af1-207">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="40af1-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="40af1-208">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="40af1-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="40af1-209">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="40af1-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="40af1-210">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="40af1-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="40af1-211">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="40af1-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="40af1-212">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="40af1-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="40af1-213">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="40af1-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="40af1-214">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="40af1-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="40af1-215">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="40af1-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="40af1-216">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="40af1-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-217">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="40af1-218">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="40af1-219">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40af1-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="40af1-220">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="40af1-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="40af1-221">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="40af1-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="40af1-222">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="40af1-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="40af1-223">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="40af1-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="40af1-224">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="40af1-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="40af1-225">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="40af1-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="40af1-226">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="40af1-226">The InitialCreate class</span></span>

<span data-ttu-id="40af1-227">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="40af1-228">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="40af1-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="40af1-229">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="40af1-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="40af1-230">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="40af1-230">Test the app</span></span>

* <span data-ttu-id="40af1-231">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="40af1-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="40af1-232">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="40af1-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-234">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="40af1-235">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="40af1-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="40af1-236">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="40af1-236">Test the **Create** page.</span></span> <span data-ttu-id="40af1-237">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="40af1-238">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="40af1-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="40af1-239">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="40af1-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="40af1-240">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="40af1-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="40af1-241">**Düzenleme** , **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="40af1-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="40af1-242">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="40af1-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-244">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="40af1-245">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="40af1-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="40af1-246">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="40af1-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-247">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="40af1-248">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="40af1-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="40af1-249">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="40af1-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="40af1-250">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="40af1-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="40af1-251">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="40af1-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="40af1-252">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="40af1-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="40af1-253">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="40af1-254">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="40af1-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="40af1-255">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="40af1-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="40af1-256">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="40af1-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="40af1-257">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="40af1-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="40af1-258">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="40af1-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="40af1-259">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="40af1-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="40af1-260">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="40af1-261">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="40af1-262">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="40af1-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="40af1-263">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="40af1-264">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="40af1-265">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="40af1-266">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="40af1-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="40af1-267">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="40af1-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="40af1-268">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="40af1-269">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="40af1-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="40af1-270">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="40af1-271">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="40af1-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="40af1-272">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="40af1-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="40af1-273">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="40af1-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="40af1-274">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="40af1-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="40af1-275">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="40af1-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="40af1-276">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="40af1-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="40af1-277">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="40af1-278">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="40af1-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="40af1-279">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="40af1-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="40af1-280">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="40af1-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="40af1-281">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="40af1-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="40af1-282">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="40af1-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="40af1-283">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="40af1-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="40af1-284">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="40af1-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40af1-285">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="40af1-285">Additional resources</span></span>

* [<span data-ttu-id="40af1-286">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="40af1-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="40af1-287">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="40af1-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="40af1-288">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="40af1-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="40af1-289">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="40af1-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-291">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class** .</span><span class="sxs-lookup"><span data-stu-id="40af1-291">Right-click the *Models* folder > **Add** > **Class** .</span></span> <span data-ttu-id="40af1-292">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="40af1-292">Name the class **Movie** .</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-293">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="40af1-294">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-294">Add a class to the *Models* folder named *Movie.cs* .</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="40af1-295">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="40af1-295">Scaffold the movie model</span></span>

<span data-ttu-id="40af1-296">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="40af1-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="40af1-297">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="40af1-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-299">**Çözüm Gezgini** , *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin** .</span><span class="sxs-lookup"><span data-stu-id="40af1-299">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item** .</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="40af1-301">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add** .</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="40af1-303">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="40af1-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="40af1-304">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="40af1-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="40af1-305">**Veri bağlamı sınıfı:** Simgeyi seçin **+** ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin</span><span class="sxs-lookup"><span data-stu-id="40af1-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* <span data-ttu-id="40af1-307">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="40af1-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="40af1-308">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="40af1-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="40af1-309">**Ekle** ’yi seçin</span><span class="sxs-lookup"><span data-stu-id="40af1-309">Select **Add**</span></span>

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="40af1-311">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="40af1-311">Visual Studio creates:</span></span>

* <span data-ttu-id="40af1-312">Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext. cs* )</span><span class="sxs-lookup"><span data-stu-id="40af1-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="40af1-313">Bir filmler denetleyicisi ( *denetleyiciler/MoviesController. cs* )</span><span class="sxs-lookup"><span data-stu-id="40af1-313">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="40af1-314">:::no-loc(Razor)::: Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme ( *Görünümler/filmler/ \* . cshtml* )</span><span class="sxs-lookup"><span data-stu-id="40af1-314">:::no-loc(Razor)::: view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="40af1-315">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="40af1-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding* .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="40af1-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="40af1-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="40af1-317">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="40af1-317">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="40af1-318">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="40af1-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="40af1-319">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="40af1-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="40af1-320">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="40af1-321">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="40af1-322">Proje dizininde bir komut penceresi açın ( *program.cs* , *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="40af1-322">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="40af1-323">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="40af1-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="40af1-324">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="40af1-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="40af1-325">Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="40af1-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPool:::no-loc(Identity)::: identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-327">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="40af1-328">Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="40af1-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="40af1-329">Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="40af1-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="40af1-330">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="40af1-330">Initial migration</span></span>

<span data-ttu-id="40af1-331">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="40af1-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="40af1-332">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-332">Add an initial migration.</span></span>
* <span data-ttu-id="40af1-333">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="40af1-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="40af1-335">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="40af1-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="40af1-337">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="40af1-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="40af1-338">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="40af1-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="40af1-339">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="40af1-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="40af1-340">`Initial`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="40af1-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="40af1-341">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="40af1-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="40af1-342">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="40af1-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="40af1-343">`Update-Database`Komutu, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="40af1-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-344">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="40af1-345">`ef migrations add InitialCreate`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="40af1-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="40af1-346">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="40af1-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="40af1-347">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="40af1-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="40af1-348">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="40af1-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="40af1-349">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="40af1-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="40af1-350">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="40af1-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40af1-351">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="40af1-352">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="40af1-352">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="40af1-353">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="40af1-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="40af1-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40af1-355">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.</span><span class="sxs-lookup"><span data-stu-id="40af1-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="40af1-356">Aşağıdaki yöntemi inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="40af1-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="40af1-357">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="40af1-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="40af1-358">`MvcMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="40af1-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="40af1-359">Veri bağlamı ( `MvcMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="40af1-360">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="40af1-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="40af1-361">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="40af1-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="40af1-362">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="40af1-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="40af1-363">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="40af1-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="40af1-364">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="40af1-365">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="40af1-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="40af1-366">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40af1-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="40af1-367">Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="40af1-368">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="40af1-368">Test the app</span></span>

* <span data-ttu-id="40af1-369">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="40af1-370">Aşağıdakine benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="40af1-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="40af1-371">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="40af1-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="40af1-372">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="40af1-372">Test the **Create** link.</span></span> <span data-ttu-id="40af1-373">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="40af1-374">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="40af1-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="40af1-375">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="40af1-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="40af1-376">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="40af1-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="40af1-377">**Düzenle** , **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="40af1-377">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="40af1-378">Sınıfı inceleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="40af1-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="40af1-379">Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="40af1-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="40af1-380">`services.AddDbContext<MvcMovieContext>(options =>` kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="40af1-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="40af1-381">`=>` bir [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="40af1-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="40af1-382">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="40af1-383">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="40af1-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="40af1-384">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="40af1-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="40af1-385">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="40af1-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="40af1-386">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="40af1-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="40af1-387">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="40af1-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="40af1-388">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="40af1-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="40af1-389">Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="40af1-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="40af1-390">Yapı iskelesi mekanizması, bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek), `MoviesController` Yöntem ve görünümleri oluştururken sınıfı ve görünümleri ile kullandı.</span><span class="sxs-lookup"><span data-stu-id="40af1-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="40af1-391">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="40af1-392">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="40af1-393">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="40af1-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="40af1-394">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="40af1-395">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="40af1-396">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="40af1-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="40af1-397">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="40af1-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="40af1-398">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="40af1-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="40af1-399">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="40af1-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="40af1-400">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="40af1-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="40af1-401">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="40af1-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="40af1-402">`@model`Görünüm dosyasının üst kısmına bir ifade ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="40af1-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="40af1-403">Film denetleyicisini oluştururken, `@model` *Ayrıntılar. cshtml* dosyasının en üstüne aşağıdaki ifade otomatik olarak eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="40af1-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="40af1-404">Bu `@model` yönerge, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="40af1-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="40af1-405">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="40af1-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="40af1-406">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="40af1-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="40af1-407">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="40af1-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="40af1-408">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="40af1-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="40af1-409">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="40af1-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="40af1-410">Film denetleyicisini oluştururken, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi otomatik olarak dahil edin:</span><span class="sxs-lookup"><span data-stu-id="40af1-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="40af1-411">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="40af1-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="40af1-412">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="40af1-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="40af1-413">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="40af1-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="40af1-414">Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="40af1-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40af1-415">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="40af1-415">Additional resources</span></span>

* [<span data-ttu-id="40af1-416">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="40af1-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="40af1-417">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="40af1-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="40af1-418">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Bir sonraki veritabanıyla çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="40af1-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
