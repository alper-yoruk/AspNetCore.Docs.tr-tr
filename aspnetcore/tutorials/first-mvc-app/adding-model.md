---
title: 4. bölüm, ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: ASP.NET Core MVC 'deki öğretici serisinin 4. bölümü.
ms.author: riande
ms.date: 11/16/2020
no-loc:
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855527"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="46d96-103">4. bölüm, ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="46d96-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="46d96-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="46d96-105">Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="46d96-106">Bu sınıflar, **d** VC uygulamasının "**d** odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="46d96-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="46d96-107">Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="46d96-108">EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="46d96-109">Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P** Lain **C** **O**) **O**.</span><span class="sxs-lookup"><span data-stu-id="46d96-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="46d96-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="46d96-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="46d96-111">Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="46d96-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-114">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="46d96-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="46d96-115">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="46d96-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46d96-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46d96-119">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="46d96-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="46d96-120">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="46d96-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="46d96-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="46d96-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="46d96-122">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="46d96-123"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="46d96-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="46d96-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="46d96-124">With this attribute:</span></span>

* <span data-ttu-id="46d96-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="46d96-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="46d96-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="46d96-127">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="46d96-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="46d96-128">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-130">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="46d96-132">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="46d96-133">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="46d96-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="46d96-134">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="46d96-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="46d96-135">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46d96-138">**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="46d96-139">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="46d96-140">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="46d96-142">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="46d96-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-143">Press the **Ok** button.</span></span>

<span data-ttu-id="46d96-144">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="46d96-145">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="46d96-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="46d96-147">Aşağıdaki .NET CLı komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="46d96-148">Yukarıdaki komut [ASPNET-CodeGenerator scafkatlama aracını](xref:fundamentals/tools/dotnet-aspnet-codegenerator)ekler.</span><span class="sxs-lookup"><span data-stu-id="46d96-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="46d96-149">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="46d96-149">Create a database context class</span></span>

<span data-ttu-id="46d96-150">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="46d96-151">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="46d96-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="46d96-152">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="46d96-152">Create a *Data* folder.</span></span>

<span data-ttu-id="46d96-153">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="46d96-154">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="46d96-155">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="46d96-156">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="46d96-157">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="46d96-157">Register the database context</span></span>

<span data-ttu-id="46d96-158">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="46d96-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46d96-159">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="46d96-160">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="46d96-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="46d96-161">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="46d96-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="46d96-162">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="46d96-163">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="46d96-164">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46d96-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-166">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="46d96-167">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="46d96-168">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="46d96-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="46d96-169">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="46d96-169">Add a database connection string</span></span>

<span data-ttu-id="46d96-170">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="46d96-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-172">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="46d96-173">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="46d96-174">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="46d96-174">Scaffold movie pages</span></span>

<span data-ttu-id="46d96-175">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="46d96-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-177">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="46d96-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="46d96-179">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="46d96-181">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="46d96-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="46d96-182">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="46d96-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="46d96-183">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="46d96-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc5.png)

* <span data-ttu-id="46d96-185">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="46d96-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="46d96-186">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="46d96-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="46d96-187">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="46d96-187">Select **Add**</span></span>

<span data-ttu-id="46d96-188">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="46d96-188">Visual Studio creates:</span></span>

* <span data-ttu-id="46d96-189">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="46d96-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="46d96-190">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="46d96-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="46d96-191">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="46d96-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="46d96-193">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="46d96-194">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="46d96-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="46d96-195">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-196">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46d96-197">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="46d96-198">Yapı iskelesi aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="46d96-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="46d96-199">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="46d96-200">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="46d96-201">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="46d96-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="46d96-202">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="46d96-202">Initial migration</span></span>

<span data-ttu-id="46d96-203">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="46d96-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="46d96-204">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-206">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="46d96-207">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="46d96-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="46d96-208">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="46d96-209">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="46d96-210">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="46d96-211">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="46d96-212">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="46d96-213">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="46d96-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="46d96-214">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="46d96-215">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="46d96-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="46d96-216">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="46d96-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="46d96-217">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="46d96-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="46d96-218">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="46d96-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="46d96-219">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="46d96-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-220">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="46d96-221">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="46d96-222">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="46d96-223">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="46d96-224">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="46d96-225">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="46d96-226">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="46d96-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="46d96-227">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="46d96-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="46d96-228">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="46d96-229">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="46d96-229">The InitialCreate class</span></span>

<span data-ttu-id="46d96-230">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="46d96-231">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="46d96-232">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="46d96-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="46d96-233">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="46d96-233">Test the app</span></span>

* <span data-ttu-id="46d96-234">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="46d96-235">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="46d96-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-237">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="46d96-238">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="46d96-239">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-239">Test the **Create** page.</span></span> <span data-ttu-id="46d96-240">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="46d96-241">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="46d96-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="46d96-242">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="46d96-243">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="46d96-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="46d96-244">**Düzenleme**, **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="46d96-245">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-247">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="46d96-248">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="46d96-249">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-250">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="46d96-251">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="46d96-252">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="46d96-253">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="46d96-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="46d96-254">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="46d96-255">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="46d96-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="46d96-256">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="46d96-257">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="46d96-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="46d96-258">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="46d96-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="46d96-259">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="46d96-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="46d96-260">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="46d96-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="46d96-261">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="46d96-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="46d96-262">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="46d96-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="46d96-263">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="46d96-264">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="46d96-265">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="46d96-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="46d96-266">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="46d96-267">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="46d96-268">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="46d96-269">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="46d96-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="46d96-270">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="46d96-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="46d96-271">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="46d96-272">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="46d96-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="46d96-273">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="46d96-274">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="46d96-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="46d96-275">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="46d96-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="46d96-276">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="46d96-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="46d96-277">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="46d96-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="46d96-278">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="46d96-279">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="46d96-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="46d96-280">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="46d96-281">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="46d96-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="46d96-282">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="46d96-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="46d96-283">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="46d96-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="46d96-284">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="46d96-285">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="46d96-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="46d96-286">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="46d96-287">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46d96-288">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="46d96-288">Additional resources</span></span>

* [<span data-ttu-id="46d96-289">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="46d96-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="46d96-290">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="46d96-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="46d96-291">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="46d96-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="46d96-292">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-294">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="46d96-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="46d96-295">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="46d96-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46d96-297">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-298">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46d96-299">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="46d96-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="46d96-300">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="46d96-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="46d96-301">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="46d96-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="46d96-302">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="46d96-303"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="46d96-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="46d96-304">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="46d96-304">With this attribute:</span></span>

* <span data-ttu-id="46d96-305">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="46d96-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="46d96-306">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="46d96-307">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="46d96-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="46d96-308">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-310">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="46d96-312">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="46d96-313">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="46d96-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="46d96-314">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="46d96-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="46d96-315">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-317">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46d96-318">**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="46d96-319">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="46d96-320">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="46d96-322">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="46d96-323">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="46d96-323">Press the **Ok** button.</span></span>

<span data-ttu-id="46d96-324">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="46d96-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="46d96-325">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="46d96-326">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="46d96-327">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="46d96-327">Create a database context class</span></span>

<span data-ttu-id="46d96-328">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="46d96-329">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="46d96-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="46d96-330">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="46d96-330">Create a *Data* folder.</span></span>

<span data-ttu-id="46d96-331">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="46d96-332">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="46d96-333">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="46d96-334">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="46d96-335">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="46d96-335">Register the database context</span></span>

<span data-ttu-id="46d96-336">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="46d96-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46d96-337">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="46d96-338">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="46d96-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="46d96-339">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="46d96-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="46d96-340">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="46d96-341">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="46d96-342">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46d96-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-344">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="46d96-345">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="46d96-346">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="46d96-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="46d96-347">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="46d96-347">Add a database connection string</span></span>

<span data-ttu-id="46d96-348">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="46d96-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-350">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="46d96-351">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="46d96-352">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="46d96-352">Scaffold movie pages</span></span>

<span data-ttu-id="46d96-353">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="46d96-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-355">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="46d96-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="46d96-357">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="46d96-359">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="46d96-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="46d96-360">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="46d96-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="46d96-361">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="46d96-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* <span data-ttu-id="46d96-363">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="46d96-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="46d96-364">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="46d96-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="46d96-365">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="46d96-365">Select **Add**</span></span>

<span data-ttu-id="46d96-366">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="46d96-366">Visual Studio creates:</span></span>

* <span data-ttu-id="46d96-367">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="46d96-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="46d96-368">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="46d96-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="46d96-369">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="46d96-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="46d96-371">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="46d96-372">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="46d96-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="46d96-373">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-374">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46d96-375">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="46d96-376">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="46d96-377">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="46d96-378">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="46d96-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="46d96-379">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="46d96-379">Initial migration</span></span>

<span data-ttu-id="46d96-380">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="46d96-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="46d96-381">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-383">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="46d96-384">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="46d96-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="46d96-385">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="46d96-386">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="46d96-387">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="46d96-388">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="46d96-389">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="46d96-390">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="46d96-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="46d96-391">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="46d96-392">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="46d96-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="46d96-393">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="46d96-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="46d96-394">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="46d96-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="46d96-395">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="46d96-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="46d96-396">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="46d96-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-397">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="46d96-398">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="46d96-399">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="46d96-400">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="46d96-401">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="46d96-402">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="46d96-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="46d96-403">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="46d96-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="46d96-404">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="46d96-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="46d96-405">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="46d96-406">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="46d96-406">The InitialCreate class</span></span>

<span data-ttu-id="46d96-407">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="46d96-408">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="46d96-409">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="46d96-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="46d96-410">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="46d96-410">Test the app</span></span>

* <span data-ttu-id="46d96-411">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="46d96-412">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="46d96-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-414">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="46d96-415">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="46d96-416">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-416">Test the **Create** page.</span></span> <span data-ttu-id="46d96-417">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="46d96-418">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="46d96-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="46d96-419">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="46d96-420">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="46d96-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="46d96-421">**Düzenleme**, **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="46d96-422">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-424">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="46d96-425">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="46d96-426">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-427">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="46d96-428">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="46d96-429">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="46d96-430">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="46d96-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="46d96-431">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="46d96-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="46d96-432">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="46d96-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="46d96-433">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="46d96-434">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="46d96-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="46d96-435">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="46d96-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="46d96-436">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="46d96-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="46d96-437">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="46d96-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="46d96-438">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="46d96-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="46d96-439">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="46d96-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="46d96-440">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="46d96-441">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="46d96-442">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="46d96-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="46d96-443">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="46d96-444">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="46d96-445">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="46d96-446">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="46d96-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="46d96-447">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="46d96-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="46d96-448">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="46d96-449">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="46d96-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="46d96-450">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="46d96-451">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="46d96-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="46d96-452">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="46d96-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="46d96-453">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="46d96-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="46d96-454">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="46d96-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="46d96-455">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="46d96-456">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="46d96-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="46d96-457">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="46d96-458">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="46d96-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="46d96-459">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="46d96-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="46d96-460">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="46d96-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="46d96-461">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="46d96-462">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="46d96-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="46d96-463">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="46d96-464">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46d96-465">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="46d96-465">Additional resources</span></span>

* [<span data-ttu-id="46d96-466">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="46d96-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="46d96-467">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="46d96-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="46d96-468">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="46d96-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="46d96-469">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="46d96-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-471">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="46d96-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="46d96-472">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="46d96-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-473">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="46d96-474">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="46d96-475">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="46d96-475">Scaffold the movie model</span></span>

<span data-ttu-id="46d96-476">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="46d96-477">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="46d96-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-479">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="46d96-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="46d96-481">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="46d96-483">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="46d96-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="46d96-484">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="46d96-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="46d96-485">**Veri bağlamı sınıfı:** Simgeyi seçin **+** ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin</span><span class="sxs-lookup"><span data-stu-id="46d96-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* <span data-ttu-id="46d96-487">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="46d96-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="46d96-488">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="46d96-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="46d96-489">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="46d96-489">Select **Add**</span></span>

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="46d96-491">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="46d96-491">Visual Studio creates:</span></span>

* <span data-ttu-id="46d96-492">Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="46d96-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="46d96-493">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="46d96-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="46d96-494">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="46d96-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="46d96-495">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="46d96-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="46d96-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46d96-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="46d96-497">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46d96-498">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="46d96-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46d96-499">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="46d96-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="46d96-500">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="46d96-501">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46d96-502">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="46d96-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46d96-503">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="46d96-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46d96-504">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="46d96-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="46d96-505">Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="46d96-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-507">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="46d96-508">Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="46d96-509">Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="46d96-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="46d96-510">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="46d96-510">Initial migration</span></span>

<span data-ttu-id="46d96-511">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="46d96-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="46d96-512">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-512">Add an initial migration.</span></span>
* <span data-ttu-id="46d96-513">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="46d96-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="46d96-515">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="46d96-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="46d96-517">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="46d96-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="46d96-518">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="46d96-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="46d96-519">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="46d96-520">`Initial`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="46d96-521">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="46d96-522">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="46d96-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="46d96-523">`Update-Database`Komutu, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="46d96-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-524">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="46d96-525">`ef migrations add InitialCreate`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="46d96-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="46d96-526">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="46d96-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="46d96-527">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="46d96-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="46d96-528">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="46d96-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="46d96-529">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="46d96-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="46d96-530">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="46d96-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46d96-531">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="46d96-532">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="46d96-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="46d96-533">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="46d96-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46d96-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46d96-535">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.</span><span class="sxs-lookup"><span data-stu-id="46d96-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="46d96-536">Aşağıdaki yöntemi inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="46d96-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="46d96-537">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="46d96-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="46d96-538">`MvcMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="46d96-539">Veri bağlamı ( `MvcMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="46d96-540">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="46d96-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="46d96-541">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="46d96-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="46d96-542">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="46d96-543">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="46d96-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="46d96-544">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="46d96-545">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="46d96-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="46d96-546">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46d96-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="46d96-547">Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="46d96-548">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="46d96-548">Test the app</span></span>

* <span data-ttu-id="46d96-549">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="46d96-550">Aşağıdakine benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="46d96-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="46d96-551">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="46d96-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="46d96-552">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-552">Test the **Create** link.</span></span> <span data-ttu-id="46d96-553">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="46d96-554">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="46d96-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="46d96-555">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="46d96-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="46d96-556">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="46d96-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="46d96-557">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="46d96-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="46d96-558">Sınıfı inceleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="46d96-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="46d96-559">Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="46d96-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="46d96-560">`services.AddDbContext<MvcMovieContext>(options =>` kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="46d96-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="46d96-561">`=>` bir [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="46d96-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="46d96-562">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="46d96-563">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="46d96-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="46d96-564">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="46d96-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="46d96-565">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="46d96-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="46d96-566">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="46d96-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="46d96-567">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="46d96-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="46d96-568">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="46d96-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="46d96-569">Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="46d96-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="46d96-570">Yapı iskelesi mekanizması, bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek), `MoviesController` Yöntem ve görünümleri oluştururken sınıfı ve görünümleri ile kullandı.</span><span class="sxs-lookup"><span data-stu-id="46d96-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="46d96-571">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="46d96-572">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="46d96-573">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="46d96-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="46d96-574">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="46d96-575">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="46d96-576">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="46d96-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="46d96-577">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="46d96-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="46d96-578">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="46d96-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="46d96-579">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="46d96-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="46d96-580">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="46d96-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="46d96-581">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="46d96-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="46d96-582">`@model`Görünüm dosyasının üst kısmına bir ifade ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="46d96-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="46d96-583">Film denetleyicisini oluştururken, `@model` *Ayrıntılar. cshtml* dosyasının en üstüne aşağıdaki ifade otomatik olarak eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="46d96-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="46d96-584">Bu `@model` yönerge, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="46d96-585">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="46d96-586">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="46d96-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="46d96-587">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="46d96-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="46d96-588">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="46d96-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="46d96-589">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="46d96-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="46d96-590">Film denetleyicisini oluştururken, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi otomatik olarak dahil edin:</span><span class="sxs-lookup"><span data-stu-id="46d96-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="46d96-591">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="46d96-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="46d96-592">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="46d96-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="46d96-593">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="46d96-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="46d96-594">Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="46d96-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46d96-595">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="46d96-595">Additional resources</span></span>

* [<span data-ttu-id="46d96-596">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="46d96-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="46d96-597">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="46d96-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="46d96-598">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Bir sonraki veritabanıyla çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="46d96-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
