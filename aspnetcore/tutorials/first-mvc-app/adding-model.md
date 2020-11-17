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
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688455"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d06d1-103">4. bölüm, ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d06d1-104">[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="d06d1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d06d1-105">Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="d06d1-106">Bu sınıflar, **d** VC uygulamasının "**d** odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="d06d1-107">Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="d06d1-108">EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="d06d1-109">Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P** Lain **C** **O**) **O**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d06d1-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d06d1-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="d06d1-111">Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d06d1-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-114">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d06d1-115">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d06d1-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d06d1-119">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="d06d1-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="d06d1-120">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="d06d1-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="d06d1-122">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="d06d1-123"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d06d1-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d06d1-124">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="d06d1-124">With this attribute:</span></span>

* <span data-ttu-id="d06d1-125">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="d06d1-126">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d06d1-127">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="d06d1-128">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-130">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d06d1-132">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="d06d1-133">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="d06d1-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="d06d1-134">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="d06d1-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="d06d1-135">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d06d1-138">**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="d06d1-139">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="d06d1-140">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="d06d1-142">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="d06d1-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-143">Press the **Ok** button.</span></span>

<span data-ttu-id="d06d1-144">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="d06d1-145">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="d06d1-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="d06d1-147">Aşağıdaki .NET CLı komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="d06d1-148">Yukarıdaki komut [ASPNET-CodeGenerator scafkatlama aracını](xref:fundamentals/tools/dotnet-aspnet-codegenerator)ekler.</span><span class="sxs-lookup"><span data-stu-id="d06d1-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="d06d1-149">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d06d1-149">Create a database context class</span></span>

<span data-ttu-id="d06d1-150">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="d06d1-151">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="d06d1-152">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d06d1-152">Create a *Data* folder.</span></span>

<span data-ttu-id="d06d1-153">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="d06d1-154">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d06d1-155">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d06d1-156">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="d06d1-157">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="d06d1-157">Register the database context</span></span>

<span data-ttu-id="d06d1-158">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d06d1-159">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="d06d1-160">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d06d1-161">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="d06d1-162">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="d06d1-163">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d06d1-164">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-166">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="d06d1-167">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d06d1-168">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="d06d1-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="d06d1-169">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="d06d1-169">Add a database connection string</span></span>

<span data-ttu-id="d06d1-170">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d06d1-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-172">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="d06d1-173">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="d06d1-174">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="d06d1-174">Scaffold movie pages</span></span>

<span data-ttu-id="d06d1-175">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-177">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="d06d1-179">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d06d1-181">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="d06d1-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d06d1-182">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="d06d1-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d06d1-183">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="d06d1-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc5.png)

* <span data-ttu-id="d06d1-185">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d06d1-186">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d06d1-187">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="d06d1-187">Select **Add**</span></span>

<span data-ttu-id="d06d1-188">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d06d1-188">Visual Studio creates:</span></span>

* <span data-ttu-id="d06d1-189">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d06d1-190">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d06d1-191">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="d06d1-193">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d06d1-194">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d06d1-195">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-196">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d06d1-197">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d06d1-198">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="d06d1-199">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="d06d1-200">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="d06d1-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="d06d1-201">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="d06d1-201">Initial migration</span></span>

<span data-ttu-id="d06d1-202">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="d06d1-203">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-205">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="d06d1-206">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="d06d1-207">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d06d1-208">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d06d1-209">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d06d1-210">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d06d1-211">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="d06d1-212">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d06d1-213">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="d06d1-214">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="d06d1-215">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="d06d1-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d06d1-216">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d06d1-217">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="d06d1-218">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-219">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d06d1-220">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="d06d1-221">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d06d1-222">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d06d1-223">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d06d1-224">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d06d1-225">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="d06d1-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="d06d1-226">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d06d1-227">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="d06d1-228">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="d06d1-228">The InitialCreate class</span></span>

<span data-ttu-id="d06d1-229">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="d06d1-230">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="d06d1-231">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d06d1-232">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="d06d1-232">Test the app</span></span>

* <span data-ttu-id="d06d1-233">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="d06d1-234">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="d06d1-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-236">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="d06d1-237">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="d06d1-238">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-238">Test the **Create** page.</span></span> <span data-ttu-id="d06d1-239">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d06d1-240">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d06d1-241">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d06d1-242">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-242">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d06d1-243">**Düzenleme**, **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="d06d1-244">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-244">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-246">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d06d1-247">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d06d1-248">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-249">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d06d1-250">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d06d1-251">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d06d1-252">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="d06d1-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d06d1-253">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d06d1-254">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="d06d1-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d06d1-255">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d06d1-256">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="d06d1-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d06d1-257">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d06d1-258">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d06d1-259">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d06d1-260">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="d06d1-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="d06d1-261">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="d06d1-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="d06d1-262">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d06d1-263">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d06d1-264">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d06d1-265">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d06d1-266">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d06d1-267">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d06d1-268">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d06d1-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d06d1-269">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d06d1-270">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d06d1-271">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="d06d1-272">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d06d1-273">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="d06d1-274">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d06d1-275">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="d06d1-276">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="d06d1-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="d06d1-277">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d06d1-278">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d06d1-279">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d06d1-280">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d06d1-281">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d06d1-282">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="d06d1-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d06d1-283">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d06d1-284">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d06d1-285">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d06d1-286">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d06d1-287">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d06d1-287">Additional resources</span></span>

* [<span data-ttu-id="d06d1-288">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="d06d1-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d06d1-289">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="d06d1-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d06d1-290">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d06d1-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d06d1-291">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-291">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-293">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d06d1-294">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-294">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d06d1-296">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-296">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-297">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-297">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d06d1-298">*Modeller* klasörüne sağ tıklayıp **Add**  >  **Yeni sınıf**  >  **boş sınıfı** Ekle >.</span><span class="sxs-lookup"><span data-stu-id="d06d1-298">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="d06d1-299">Dosyayı *Movie.cs* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-299">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="d06d1-300">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-300">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="d06d1-301">`Movie`Sınıfı, `Id` birincil anahtar için veritabanı için gerekli olan bir alanı içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-301">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="d06d1-302"><xref:System.ComponentModel.DataAnnotations.DataType>Üzerindeki özniteliği, `ReleaseDate` veri türünü belirtir ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="d06d1-302">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d06d1-303">Bu öznitelikle:</span><span class="sxs-lookup"><span data-stu-id="d06d1-303">With this attribute:</span></span>

* <span data-ttu-id="d06d1-304">Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-304">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="d06d1-305">Zaman bilgisi değil yalnızca tarih görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-305">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d06d1-306">[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-306">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="d06d1-307">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-307">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-308">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-308">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-309">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-309">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d06d1-311">PMC 'de şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-311">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="d06d1-312">Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="d06d1-312">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="d06d1-313">Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="d06d1-313">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="d06d1-314">Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-314">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-316">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-316">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d06d1-317">**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-317">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="d06d1-318">Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-318">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="d06d1-319">Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-319">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="d06d1-321">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-321">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="d06d1-322">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-322">Press the **Ok** button.</span></span>

<span data-ttu-id="d06d1-323">Bir **Lisans kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-323">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="d06d1-324">Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-324">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="d06d1-325">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-325">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="d06d1-326">Veritabanı bağlamı sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d06d1-326">Create a database context class</span></span>

<span data-ttu-id="d06d1-327">Model için EF Core işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-327">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="d06d1-328">Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-328">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="d06d1-329">Bir *veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d06d1-329">Create a *Data* folder.</span></span>

<span data-ttu-id="d06d1-330">Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-330">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="d06d1-331">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-331">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d06d1-332">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-332">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d06d1-333">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-333">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="d06d1-334">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="d06d1-334">Register the database context</span></span>

<span data-ttu-id="d06d1-335">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d06d1-336">Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-336">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="d06d1-337">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d06d1-338">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="d06d1-339">Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-339">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="d06d1-340">Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-340">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d06d1-341">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-341">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-342">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-342">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-343">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-343">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="d06d1-344">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-344">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d06d1-345">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="d06d1-345">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="d06d1-346">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="d06d1-346">Add a database connection string</span></span>

<span data-ttu-id="d06d1-347">Dosyaya bir bağlantı dizesi ekleyin *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d06d1-347">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-348">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-348">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-349">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-349">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="d06d1-350">Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-350">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="d06d1-351">Yapı iskelesi film sayfaları</span><span class="sxs-lookup"><span data-stu-id="d06d1-351">Scaffold movie pages</span></span>

<span data-ttu-id="d06d1-352">Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-352">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-353">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-353">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-354">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-354">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="d06d1-356">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-356">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d06d1-358">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="d06d1-358">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d06d1-359">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="d06d1-359">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d06d1-360">**Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="d06d1-360">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* <span data-ttu-id="d06d1-362">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-362">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d06d1-363">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-363">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d06d1-364">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="d06d1-364">Select **Add**</span></span>

<span data-ttu-id="d06d1-365">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d06d1-365">Visual Studio creates:</span></span>

* <span data-ttu-id="d06d1-366">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-366">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d06d1-367">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-367">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d06d1-368">Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-368">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-369">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-369">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="d06d1-370">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-370">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d06d1-371">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-371">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d06d1-372">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-372">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-373">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-373">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d06d1-374">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-374">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d06d1-375">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-375">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="d06d1-376">Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-376">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="d06d1-377">Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.</span><span class="sxs-lookup"><span data-stu-id="d06d1-377">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="d06d1-378">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="d06d1-378">Initial migration</span></span>

<span data-ttu-id="d06d1-379">Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-379">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="d06d1-380">Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-380">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-381">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-382">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-382">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="d06d1-383">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-383">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="d06d1-384">`Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-384">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d06d1-385">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-385">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d06d1-386">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-386">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d06d1-387">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-387">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d06d1-388">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-388">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="d06d1-389">`Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-389">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d06d1-390">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-390">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="d06d1-391">Database Update komutu aşağıdaki uyarıyı üretir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-391">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="d06d1-392">' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="d06d1-392">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d06d1-393">Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-393">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d06d1-394">' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-394">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="d06d1-395">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-395">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-396">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-396">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d06d1-397">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-397">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="d06d1-398">`ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-398">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d06d1-399">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-399">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d06d1-400">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-400">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d06d1-401">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-401">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d06d1-402">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="d06d1-402">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="d06d1-403">`ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-403">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d06d1-404">Bu komut, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-404">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="d06d1-405">Initialcreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="d06d1-405">The InitialCreate class</span></span>

<span data-ttu-id="d06d1-406">*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-406">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="d06d1-407">`Up`Yöntemi, film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-407">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="d06d1-408">`Down`Yöntemi, geçiş tarafından yapılan şema değişikliklerini geri alır `Up` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-408">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d06d1-409">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="d06d1-409">Test the app</span></span>

* <span data-ttu-id="d06d1-410">Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-410">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="d06d1-411">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="d06d1-411">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-412">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-413">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-413">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="d06d1-414">Muhtemelen [geçişler adımını](#migration)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-414">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="d06d1-415">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-415">Test the **Create** page.</span></span> <span data-ttu-id="d06d1-416">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-416">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d06d1-417">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-417">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d06d1-418">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-418">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d06d1-419">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-419">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d06d1-420">**Düzenleme**, **Ayrıntılar** ve **silme** sayfalarını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-420">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="d06d1-421">Denetleyiciye bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-421">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-423">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-423">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d06d1-424">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-424">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d06d1-425">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-425">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-426">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-426">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d06d1-427">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-427">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d06d1-428">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-428">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d06d1-429">Geliştirme için SQLite kullanın, üretim için SQL Server</span><span class="sxs-lookup"><span data-stu-id="d06d1-429">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d06d1-430">SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-430">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d06d1-431">Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> .</span><span class="sxs-lookup"><span data-stu-id="d06d1-431">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d06d1-432">`IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-432">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d06d1-433">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="d06d1-433">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d06d1-434">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-434">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d06d1-435">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-435">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d06d1-436">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-436">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d06d1-437">Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="d06d1-437">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="d06d1-438">Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.</span><span class="sxs-lookup"><span data-stu-id="d06d1-438">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="d06d1-439">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-439">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d06d1-440">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-440">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d06d1-441">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-441">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d06d1-442">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-442">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d06d1-443">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-443">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d06d1-444">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-444">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d06d1-445">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d06d1-445">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d06d1-446">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-446">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d06d1-447">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-447">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d06d1-448">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-448">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="d06d1-449">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-449">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d06d1-450">`@model`Görünüm dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-450">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="d06d1-451">Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-451">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d06d1-452">Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-452">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="d06d1-453">`Model`Nesne kesin olarak belirlenmiş.</span><span class="sxs-lookup"><span data-stu-id="d06d1-453">The `Model` object is strongly typed.</span></span> <span data-ttu-id="d06d1-454">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-454">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d06d1-455">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-455">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d06d1-456">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-456">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d06d1-457">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-457">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d06d1-458">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-458">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d06d1-459">Film denetleyicisi oluşturulduğunda, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi içeriyordu:</span><span class="sxs-lookup"><span data-stu-id="d06d1-459">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d06d1-460">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-460">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d06d1-461">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-461">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d06d1-462">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-462">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d06d1-463">Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-463">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d06d1-464">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d06d1-464">Additional resources</span></span>

* [<span data-ttu-id="d06d1-465">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="d06d1-465">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d06d1-466">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="d06d1-466">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d06d1-467">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Daha sonra SQL Ile çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d06d1-467">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d06d1-468">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="d06d1-468">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-470">Sınıf Ekle > *modeller* klasörüne sağ tıklayın **Add**  >  **Class**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-470">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d06d1-471">Sınıf **filmi** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-471">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-472">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-472">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d06d1-473">*Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-473">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d06d1-474">Film modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="d06d1-474">Scaffold the movie model</span></span>

<span data-ttu-id="d06d1-475">Bu bölümde, film modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-475">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d06d1-476">Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-476">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-477">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-477">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-478">**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.</span><span class="sxs-lookup"><span data-stu-id="d06d1-478">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="d06d1-480">**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-480">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d06d1-482">**Denetleyici Ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="d06d1-482">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d06d1-483">**Model sınıfı:** *Film (mvcmovie. modeller)*</span><span class="sxs-lookup"><span data-stu-id="d06d1-483">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d06d1-484">**Veri bağlamı sınıfı:** Simgeyi seçin **+** ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin</span><span class="sxs-lookup"><span data-stu-id="d06d1-484">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* <span data-ttu-id="d06d1-486">**Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-486">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d06d1-487">**Denetleyici adı:** Varsayılan *MoviesController* tut</span><span class="sxs-lookup"><span data-stu-id="d06d1-487">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d06d1-488">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="d06d1-488">Select **Add**</span></span>

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="d06d1-490">Visual Studio şunları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d06d1-490">Visual Studio creates:</span></span>

* <span data-ttu-id="d06d1-491">Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-491">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="d06d1-492">Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-492">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d06d1-493">Razor Oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d06d1-493">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d06d1-494">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi* olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-494">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d06d1-495">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d06d1-495">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d06d1-496">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-496">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="d06d1-497">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="d06d1-497">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="d06d1-498">Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-498">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d06d1-499">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-499">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-500">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-500">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d06d1-501">Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="d06d1-501">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="d06d1-502">Scafkatlama aracını yükler:</span><span class="sxs-lookup"><span data-stu-id="d06d1-502">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="d06d1-503">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d06d1-503">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="d06d1-504">Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="d06d1-504">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-505">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-506">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-506">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="d06d1-507">Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-507">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="d06d1-508">Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-508">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d06d1-509">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="d06d1-509">Initial migration</span></span>

<span data-ttu-id="d06d1-510">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="d06d1-510">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="d06d1-511">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-511">Add an initial migration.</span></span>
* <span data-ttu-id="d06d1-512">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-512">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-513">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-513">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d06d1-514">**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-514">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="d06d1-516">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-516">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="d06d1-517">`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-517">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="d06d1-518">Veritabanı şeması, sınıfında belirtilen modeli temel alır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-518">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="d06d1-519">`Initial`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-519">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="d06d1-520">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-520">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="d06d1-521">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="d06d1-521">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="d06d1-522">`Update-Database`Komutu, `Up` veritabanını oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-522">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-523">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-523">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="d06d1-524">`ef migrations add InitialCreate`Komut, ilk veritabanı şemasını oluşturmak için kod üretir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-524">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="d06d1-525">Veritabanı şeması sınıfında belirtilen modeli temel alır `MvcMovieContext` ( *Data/MvcMovieContext. cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="d06d1-525">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="d06d1-526">`InitialCreate`Bağımsız değişkeni geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-526">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d06d1-527">Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-527">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d06d1-528">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="d06d1-528">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d06d1-529">ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-529">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d06d1-530">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-530">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="d06d1-531">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-531">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d06d1-532">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-532">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d06d1-533">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-533">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d06d1-534">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.</span><span class="sxs-lookup"><span data-stu-id="d06d1-534">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="d06d1-535">Aşağıdaki yöntemi inceleyin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-535">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d06d1-536">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="d06d1-536">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="d06d1-537">`MvcMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-537">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="d06d1-538">Veri bağlamı ( `MvcMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-538">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d06d1-539">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-539">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="d06d1-540">Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d06d1-540">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d06d1-541">Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-541">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d06d1-542">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-542">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d06d1-543">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-543">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d06d1-544">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="d06d1-544">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d06d1-545">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d06d1-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d06d1-546">Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-546">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d06d1-547">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="d06d1-547">Test the app</span></span>

* <span data-ttu-id="d06d1-548">Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-548">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d06d1-549">Aşağıdakine benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="d06d1-549">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d06d1-550">[Geçişler adımını](#pmc)kaçırdınız.</span><span class="sxs-lookup"><span data-stu-id="d06d1-550">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d06d1-551">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-551">Test the **Create** link.</span></span> <span data-ttu-id="d06d1-552">Veri girin ve gönderebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-552">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d06d1-553">Alana ondalık virgüller giremeyebilirsiniz `Price` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-553">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d06d1-554">Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-554">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d06d1-555">Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="d06d1-555">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d06d1-556">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-556">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d06d1-557">Sınıfı inceleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-557">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="d06d1-558">Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-558">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="d06d1-559">`services.AddDbContext<MvcMovieContext>(options =>` kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-559">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="d06d1-560">`=>` bir [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="d06d1-560">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="d06d1-561">*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-561">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d06d1-562">Oluşturucu, veritabanı bağlamını () denetleyiciye eklemek için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır `MvcMovieContext` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-562">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d06d1-563">Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-563">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d06d1-564">Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü</span><span class="sxs-lookup"><span data-stu-id="d06d1-564">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d06d1-565">Bu öğreticide daha önce, bir denetleyicinin sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-565">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d06d1-566">`ViewData`Sözlük, bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-566">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d06d1-567">MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="d06d1-567">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d06d1-568">Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar.</span><span class="sxs-lookup"><span data-stu-id="d06d1-568">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="d06d1-569">Yapı iskelesi mekanizması, bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek), `MoviesController` Yöntem ve görünümleri oluştururken sınıfı ve görünümleri ile kullandı.</span><span class="sxs-lookup"><span data-stu-id="d06d1-569">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="d06d1-570">`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-570">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d06d1-571">`id`Parametre genellikle rota verileri olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-571">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d06d1-572">Örneğin `https://localhost:5001/movies/details/1` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-572">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d06d1-573">Denetleyicinin denetleyicisi `movies` (Ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-573">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d06d1-574">Eylem `details` (ıkıncı URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-574">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d06d1-575">Kimliği 1 ' e (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="d06d1-575">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d06d1-576">Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="d06d1-576">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d06d1-577">Parametre, bir `id` kimlik değeri sağlanmadığından [null yapılabilir bir tür](/dotnet/csharp/programming-guide/nullable-types/index) () olarak tanımlanır `int?` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-577">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d06d1-578">Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `FirstOrDefaultAsync` , rota verileriyle veya sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-578">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d06d1-579">Bir film bulunursa, `Movie` görünüme bir model örneği geçirilir `Details` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-579">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="d06d1-580">*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-580">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d06d1-581">`@model`Görünüm dosyasının üst kısmına bir ifade ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d06d1-581">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="d06d1-582">Film denetleyicisini oluştururken, `@model` *Ayrıntılar. cshtml* dosyasının en üstüne aşağıdaki ifade otomatik olarak eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-582">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d06d1-583">Bu `@model` yönerge, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-583">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d06d1-584">Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` türü kesin belirlenmiş nesne ile HTML yardımcılarının içine geçirir `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-584">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d06d1-585">`Create`Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.</span><span class="sxs-lookup"><span data-stu-id="d06d1-585">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d06d1-586">*Index. cshtml* görünümünü ve `Index` filmler denetleyicisindeki yöntemini inceleyin.</span><span class="sxs-lookup"><span data-stu-id="d06d1-586">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d06d1-587">Yöntemi çağırdığında kodun nasıl bir nesne oluşturduğunu fark edin `List` `View` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-587">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d06d1-588">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-588">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d06d1-589">Film denetleyicisini oluştururken, yapı iskelesi `@model` *Index. cshtml* dosyasının en üstüne aşağıdaki ifadeyi otomatik olarak dahil edin:</span><span class="sxs-lookup"><span data-stu-id="d06d1-589">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d06d1-590">`@model`Yönergesi, kesin olarak belirlenmiş bir nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar `Model` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-590">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d06d1-591">Örneğin, *Index. cshtml* görünümünde, kod `foreach` kesin türü belirtilmiş nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır `Model` :</span><span class="sxs-lookup"><span data-stu-id="d06d1-591">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d06d1-592">`Model`Nesne türü kesin belirlenmiş olduğundan (bir nesne olarak `IEnumerable<Movie>` ), döngüdeki her öğe olarak yazılır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="d06d1-592">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d06d1-593">Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="d06d1-593">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d06d1-594">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d06d1-594">Additional resources</span></span>

* [<span data-ttu-id="d06d1-595">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="d06d1-595">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d06d1-596">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="d06d1-596">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d06d1-597">[Önceki bir görünüm ekleniyor](adding-view.md) 
>  [Bir sonraki veritabanıyla çalışma](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d06d1-597">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
