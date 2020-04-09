---
title: ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: Basit bir ASP.NET Core uygulamasına bir model ekleyin.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434518"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f459e-103">ASP.NET Core MVC uygulamasına model ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f459e-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f459e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f459e-105">Bu bölümde, bir veritabanında film yönetmek için sınıflar ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="f459e-106">Bu sınıflar **M**VC uygulamasının **"M**odel" parçası olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f459e-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="f459e-107">Bir veritabanı ile çalışmak için Bu sınıfları [Entity Framework Core](/ef/core) (EF Core) ile kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="f459e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="f459e-108">EF Core, yazmanız gereken veri erişim kodunu basitleştiren nesne ilişkisisel eşleme (ORM) çerçevesidir.</span><span class="sxs-lookup"><span data-stu-id="f459e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="f459e-109">Oluşturduğunuz model sınıfları POCO sınıfları olarak bilinir **(P**lain **O**ld **C**LR **O**bjects'ten) çünkü EF Core'a herhangi bir bağımlılıkları yoktur.</span><span class="sxs-lookup"><span data-stu-id="f459e-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f459e-110">Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlarlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="f459e-111">Bu öğreticide, önce model sınıflarını yazarsınız ve EF Core veritabanını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="f459e-112">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-114">**Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="f459e-115">Dosyayı *Movie.cs.*</span><span class="sxs-lookup"><span data-stu-id="f459e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f459e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f459e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f459e-117">*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f459e-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f459e-119">**Yeni**Sınıf > **Boş Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="f459e-120">Dosyayı *Movie.cs.*</span><span class="sxs-lookup"><span data-stu-id="f459e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="f459e-121">*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="f459e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="f459e-122">Sınıf, `Movie` birincil `Id` anahtar için veritabanı tarafından gerekli olan bir alan içerir.</span><span class="sxs-lookup"><span data-stu-id="f459e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="f459e-123">Veri <xref:System.ComponentModel.DataAnnotations.DataType> türüne `ReleaseDate` ilişkin öznitelik (`Date`)</span><span class="sxs-lookup"><span data-stu-id="f459e-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f459e-124">Bu öznitelik ile:</span><span class="sxs-lookup"><span data-stu-id="f459e-124">With this attribute:</span></span>

* <span data-ttu-id="f459e-125">Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="f459e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="f459e-126">Yalnızca tarih görüntülenir, saat bilgisi değil.</span><span class="sxs-lookup"><span data-stu-id="f459e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f459e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="f459e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="f459e-128">NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-130">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f459e-132">PMC'de aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="f459e-133">Önceki komut, EF Core SQL Server sağlayıcısını ekler.</span><span class="sxs-lookup"><span data-stu-id="f459e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="f459e-134">Sağlayıcı paketi, EF Core paketini bağımlılık olarak yükler.</span><span class="sxs-lookup"><span data-stu-id="f459e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="f459e-135">Ek paketler, öğreticinin ilerleyen saatlerinde iskele adımına otomatik olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="f459e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f459e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f459e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f459e-137">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f459e-138">**Proje** menüsünden **NuGet Paketlerini Yönet'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="f459e-139">Sağ üstteki **Arama** alanına girin `Microsoft.EntityFrameworkCore.SQLite` ve aramak için **İade** tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="f459e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="f459e-140">Eşleşen NuGet paketini seçin ve **Paket Ekle** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="f459e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Varlık Çerçeve Çekirdek NuGet Paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="f459e-142">**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçili olarak görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f459e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="f459e-143">**Tamam** düğmesine basın.</span><span class="sxs-lookup"><span data-stu-id="f459e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="f459e-144">**Lisans Kabul** iletişim kutusu görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="f459e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="f459e-145">Lisansları istediğiniz gibi gözden geçirin ve **ardından Kabul Et** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="f459e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="f459e-146">Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları tekrarlayın:</span><span class="sxs-lookup"><span data-stu-id="f459e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="f459e-147">Veritabanı bağlam sınıfı oluşturma</span><span class="sxs-lookup"><span data-stu-id="f459e-147">Create a database context class</span></span>

<span data-ttu-id="f459e-148">`Movie` Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil) koordine etmek için bir veritabanı bağlam ı gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f459e-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="f459e-149">Veritabanı bağlamı [Microsoft.EntityFrameworkCore.DbContext'den](/dotnet/api/microsoft.entityframeworkcore.dbcontext) türetilmiştir ve veri modeline dahil olacak varlıkları belirtir.</span><span class="sxs-lookup"><span data-stu-id="f459e-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="f459e-150">Bir *Veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f459e-150">Create a *Data* folder.</span></span>

<span data-ttu-id="f459e-151">Aşağıdaki kodile bir *Veri/MvcMovieContext.cs* dosyası ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="f459e-152">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f459e-153">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f459e-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f459e-154">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f459e-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="f459e-155">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="f459e-155">Register the database context</span></span>

<span data-ttu-id="f459e-156">ASP.NET Core [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f459e-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f459e-157">Hizmetler (EF Core DB bağlamı gibi) uygulama nın başlatılması sırasında DI'ye kaydedilmelidir.</span><span class="sxs-lookup"><span data-stu-id="f459e-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="f459e-158">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f459e-159">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="f459e-160">Bu bölümde, veritabanı bağlamını DI kapsayıcısı ile kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f459e-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="f459e-161">Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f459e-162">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-164">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="f459e-165">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f459e-166">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="f459e-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="f459e-167">Veritabanı bağlantı dizesi ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-167">Add a database connection string</span></span>

<span data-ttu-id="f459e-168">*appsettings.json* dosyasına bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-170">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="f459e-171">Derleyici hataları için bir denetim olarak proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f459e-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="f459e-172">İskele film sayfaları</span><span class="sxs-lookup"><span data-stu-id="f459e-172">Scaffold movie pages</span></span>

<span data-ttu-id="f459e-173">Film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) sayfalarını oluşturmak için iskele aracını kullanın.</span><span class="sxs-lookup"><span data-stu-id="f459e-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-175">**Çözüm Gezgini'nde,** Yeni İskele Öğesi > **ekle>** *Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="f459e-177">İskele **Ekle** iletişim kutusunda, **Entity Framework > Add'i kullanarak görünümlü MVC**Denetleyicisi'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![İskele iletişim kutusu ekle](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="f459e-179">Denetleyici **Ekle** iletişim kutusunu tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="f459e-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="f459e-180">**Model sınıfı:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="f459e-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="f459e-181">**Veri bağlamı sınıfı:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="f459e-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Veri bağlamı ekleme](adding-model/_static/dc3.png)

* <span data-ttu-id="f459e-183">**Görünümler:** Her seçeneğin varsayılanını işaretli tutun</span><span class="sxs-lookup"><span data-stu-id="f459e-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="f459e-184">**Denetleyici adı:** Varsayılan *MoviesController'ı* tutun</span><span class="sxs-lookup"><span data-stu-id="f459e-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="f459e-185">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="f459e-185">Select **Add**</span></span>

<span data-ttu-id="f459e-186">Visual Studio oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f459e-186">Visual Studio creates:</span></span>

* <span data-ttu-id="f459e-187">Bir film denetleyicisi (*Denetleyiciler/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="f459e-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="f459e-188">Oluşturma, Silme, Ayrıntılar, Düzenle ve Dizin sayfaları için jilet görüntüleme dosyaları (*Görünümler/Filmler/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="f459e-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="f459e-189">Bu dosyaların otomatik oluşturulması *iskele*olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="f459e-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="f459e-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f459e-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="f459e-191">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="f459e-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="f459e-192">Linux'ta, iskele araç yolunu dışa aktarın:</span><span class="sxs-lookup"><span data-stu-id="f459e-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="f459e-193">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f459e-194">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f459e-195">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="f459e-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="f459e-196">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="f459e-197">Veritabanı yok çünkü henüz iskele sayfaları kullanamazsınız.</span><span class="sxs-lookup"><span data-stu-id="f459e-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="f459e-198">Uygulamayı çalıştırıp **Film Uygulaması** bağlantısına tıklarsanız, *açılamaz veritabanı* alırsınız veya böyle bir tablo *olmaz: Film* hatası iletisi.</span><span class="sxs-lookup"><span data-stu-id="f459e-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="f459e-199">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="f459e-199">Initial migration</span></span>

<span data-ttu-id="f459e-200">Veritabanını oluşturmak için EF Çekirdek [Geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f459e-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="f459e-201">Geçişler, veri modelinize uyacak şekilde bir veritabanı oluşturmanıza ve güncelleştirmenize izin veren araçlar kümesidir.</span><span class="sxs-lookup"><span data-stu-id="f459e-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-203">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="f459e-204">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="f459e-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="f459e-205">`Add-Migration InitialCreate`: *Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="f459e-206">Bağımsız `InitialCreate` değişken geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="f459e-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f459e-207">Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="f459e-208">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f459e-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="f459e-209">Veritabanı şeması, sınıfta belirtilen modele `MvcMovieContext` dayanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="f459e-210">`Update-Database`: Veritabanını önceki komutun oluşturduğu en son geçişle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="f459e-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="f459e-211">Bu komut, `Up` veritabanını oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="f459e-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="f459e-212">Veritabanı güncelleştirme komutu aşağıdaki uyarıyı oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f459e-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="f459e-213">Varlık türü 'Film'deki ondalık sütun 'Fiyat' için hiçbir tür belirtilmedi.</span><span class="sxs-lookup"><span data-stu-id="f459e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f459e-214">Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="f459e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f459e-215">'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin.</span><span class="sxs-lookup"><span data-stu-id="f459e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="f459e-216">Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-217">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f459e-218">Aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="f459e-219">`ef migrations add InitialCreate`: *Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyası oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="f459e-220">Bağımsız `InitialCreate` değişken geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="f459e-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f459e-221">Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="f459e-222">Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="f459e-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="f459e-223">Veritabanı şeması `MvcMovieContext` sınıfta belirtilen modele dayanır *(Data/MvcMovieContext.cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="f459e-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="f459e-224">`ef database update`: Veritabanını önceki komutun oluşturduğu en son geçişle güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="f459e-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="f459e-225">Bu komut, `Up` veritabanını oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="f459e-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="f459e-226">InitialCreate sınıfı</span><span class="sxs-lookup"><span data-stu-id="f459e-226">The InitialCreate class</span></span>

<span data-ttu-id="f459e-227">*Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyasını inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="f459e-228">Yöntem `Up` Film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f459e-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="f459e-229">Yöntem, `Down` geçiş tarafından yapılan şema değişikliklerini `Up` geri alır.</span><span class="sxs-lookup"><span data-stu-id="f459e-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="f459e-230">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f459e-230">Test the app</span></span>

* <span data-ttu-id="f459e-231">Uygulamayı çalıştırın ve **Film Uygulaması** bağlantısını tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="f459e-232">Aşağıdakilerden birine benzer bir özel durum alırsanız:</span><span class="sxs-lookup"><span data-stu-id="f459e-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-234">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="f459e-235">Muhtemelen göç [adımını kaçırmışsınızdır.](#migration)</span><span class="sxs-lookup"><span data-stu-id="f459e-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="f459e-236">**Oluştur** sayfasını test edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-236">Test the **Create** page.</span></span> <span data-ttu-id="f459e-237">Veri girin ve gönderin.</span><span class="sxs-lookup"><span data-stu-id="f459e-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="f459e-238">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f459e-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f459e-239">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f459e-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f459e-240">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="f459e-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f459e-241">**Düzenle,** **Ayrıntıları**ve **Sayfalarını Sil'i** test edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="f459e-242">Denetleyicide bağımlılık enjeksiyonu</span><span class="sxs-lookup"><span data-stu-id="f459e-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-244">*Denetleyiciler/MoviesController.cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f459e-245">Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f459e-246">Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-247">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f459e-248">Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f459e-249">Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f459e-250">Geliştirme için SQLite'ı, üretim için SQL Server'ı kullanın</span><span class="sxs-lookup"><span data-stu-id="f459e-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f459e-251">SQLite seçildiğinde, şablon oluşturulan kod geliştirmeye hazırdır.</span><span class="sxs-lookup"><span data-stu-id="f459e-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f459e-252">Aşağıdaki kod, Başlangıç'a nasıl enjekte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ediletilir gösterir.</span><span class="sxs-lookup"><span data-stu-id="f459e-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f459e-253">`IWebHostEnvironment`böylece geliştirme `ConfigureServices` ve SQL Server üretimde SQLite kullanabilirsiniz enjekte edilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="f459e-254">Güçlü bir şekilde yazılan @model modeller ve anahtar kelime</span><span class="sxs-lookup"><span data-stu-id="f459e-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="f459e-255">Bu öğreticinin başlarında, bir denetleyicinin `ViewData` sözlüğü kullanarak verileri veya nesneleri görünüme nasıl geçirebileceğini gördün.</span><span class="sxs-lookup"><span data-stu-id="f459e-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="f459e-256">Sözlük, `ViewData` bilgileri bir görünüme aktarmak için uygun bir geç bağlı yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="f459e-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="f459e-257">MVC ayrıca güçlü bir şekilde yazılan model nesnelerini bir görünüme geçirme olanağı da sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="f459e-258">Bu güçlü bir şekilde yazılan yaklaşım, zaman kodu denetiminin derlenmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="f459e-259">İskele mekanizması sınıf ve görünümler ile `MoviesController` bu yaklaşımı (yani, güçlü bir şekilde yazılan bir model geçen) kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="f459e-260">`Details` *Denetleyiciler/MoviesController.cs* dosyasında oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="f459e-261">`id` Parametre genellikle rota verisi olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="f459e-262">Örneğin `https://localhost:5001/movies/details/1` kümeler:</span><span class="sxs-lookup"><span data-stu-id="f459e-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="f459e-263">Denetleyiciye `movies` denetleyici (ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="f459e-264">Eylem `details` (ikinci URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="f459e-265">Kimlik 1 (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="f459e-266">Ayrıca aşağıdaki gibi `id` bir sorgu dize ile geçirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f459e-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="f459e-267">Bir `id` kimlik değeri sağlanmadığı durumlarda`int?`parametre , () geçersiz bir [tür](/dotnet/csharp/programming-guide/nullable-types/index) olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="f459e-268">Yol verileri veya sorgu `FirstOrDefaultAsync` dize değeriyle eşleşen film varlıklarını seçmek için [bir lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="f459e-269">Bir film bulunursa, modelin `Movie` bir örneği `Details` görünüme aktarılır:</span><span class="sxs-lookup"><span data-stu-id="f459e-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="f459e-270">*Görünümler/Filmler/Ayrıntılar.cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="f459e-271">Görünüm `@model` dosyasının üst kısmındaki deyim, görünümün beklediği nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="f459e-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="f459e-272">Film denetleyicisi oluşturulduğunda `@model` aşağıdaki ifade eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f459e-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="f459e-273">Bu `@model` yönerge, denetleyicinin görünüme geçtiği filme erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="f459e-274">Nesne `Model` güçlü bir şekilde yazılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="f459e-275">Örneğin, *Details.cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` güçlü bir şekilde yazılan `Model` nesneye sahip HTML Yardımcıları ve HTML Yardımcıları'na geçirir.</span><span class="sxs-lookup"><span data-stu-id="f459e-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="f459e-276">Ve yöntemler ve görünümler `Movie` de bir model nesnesi geçmek. `Edit` `Create`</span><span class="sxs-lookup"><span data-stu-id="f459e-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="f459e-277">Filmler denetleyicisinde *Index.cshtml* görünümünü ve `Index` yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="f459e-278">Kodun yöntemi aradığında `List` nesneyi nasıl `View` oluşturduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="f459e-279">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="f459e-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="f459e-280">Film denetleyicisi oluşturulduğunda, iskele `@model` *Index.cshtml* dosyasının üst kısmında aşağıdaki ifadeyi içerir:</span><span class="sxs-lookup"><span data-stu-id="f459e-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="f459e-281">Yönerge, `@model` güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak denetleyicinin görünüme geçtiği film listesine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f459e-282">Örneğin, *Index.cshtml* görünümünde, kod filmler arasında güçlü `foreach` bir şekilde yazılan `Model` nesnenin üzerinde bir ifadeyle döngüler:</span><span class="sxs-lookup"><span data-stu-id="f459e-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="f459e-283">`Model` Nesne güçlü bir şekilde `IEnumerable<Movie>` (nesne olarak) yazıldığı için, döngüdeki her öğe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f459e-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="f459e-284">Diğer avantajların yanı sıra, bu kodun zaman denetimini derlediğiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f459e-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f459e-285">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f459e-285">Additional resources</span></span>

* [<span data-ttu-id="f459e-286">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="f459e-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="f459e-287">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f459e-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="f459e-288">[Önceki](adding-view.md)
> [SQL ile](working-with-sql.md) Sonraki Çalışma Görünümü Ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="f459e-289">Veri modeli sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-291">**Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="f459e-292">Sınıf **Film**adı .</span><span class="sxs-lookup"><span data-stu-id="f459e-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-293">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f459e-294">*Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f459e-295">İskele film modeli</span><span class="sxs-lookup"><span data-stu-id="f459e-295">Scaffold the movie model</span></span>

<span data-ttu-id="f459e-296">Bu bölümde, film modeli iskele.</span><span class="sxs-lookup"><span data-stu-id="f459e-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f459e-297">Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-299">**Çözüm Gezgini'nde,** Yeni İskele Öğesi > **ekle>** *Denetleyiciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="f459e-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

<span data-ttu-id="f459e-301">İskele **Ekle** iletişim kutusunda, **Entity Framework > Add'i kullanarak görünümlü MVC**Denetleyicisi'ni seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![İskele iletişim kutusu ekle](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="f459e-303">Denetleyici **Ekle** iletişim kutusunu tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="f459e-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="f459e-304">**Model sınıfı:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="f459e-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="f459e-305">**Veri bağlamı sınıfı:** Simgeyi **+** seçin ve varsayılan **MvcMovie.Models.MvcMovieContext** ekleyin</span><span class="sxs-lookup"><span data-stu-id="f459e-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Veri bağlamı ekleme](adding-model/_static/dc.png)

* <span data-ttu-id="f459e-307">**Görünümler:** Her seçeneğin varsayılanını işaretli tutun</span><span class="sxs-lookup"><span data-stu-id="f459e-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="f459e-308">**Denetleyici adı:** Varsayılan *MoviesController'ı* tutun</span><span class="sxs-lookup"><span data-stu-id="f459e-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="f459e-309">**Ekle**’yi seçin</span><span class="sxs-lookup"><span data-stu-id="f459e-309">Select **Add**</span></span>

![Denetleyici ekle iletişim kutusu](adding-model/_static/add_controller2.png)

<span data-ttu-id="f459e-311">Visual Studio oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f459e-311">Visual Studio creates:</span></span>

* <span data-ttu-id="f459e-312">Varlık Çerçeve Çekirdek [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="f459e-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="f459e-313">Bir film denetleyicisi (*Denetleyiciler/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="f459e-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="f459e-314">Oluşturma, Silme, Ayrıntılar, Düzenle ve Dizin sayfaları için jilet görüntüleme dosyaları (*Görünümler/Filmler/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="f459e-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="f459e-315">Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) otomatik oluşturma (oluşturmak, okumak, güncellemek ve sil) eylem yöntemleri ve görünümleri *iskele*olarak bilinir.</span><span class="sxs-lookup"><span data-stu-id="f459e-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f459e-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f459e-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f459e-317">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="f459e-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="f459e-318">İskele aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="f459e-319">Linux'ta, iskele araç yolunu dışa aktarın:</span><span class="sxs-lookup"><span data-stu-id="f459e-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="f459e-320">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f459e-321">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f459e-322">Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).</span><span class="sxs-lookup"><span data-stu-id="f459e-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="f459e-323">İskele aracını yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="f459e-324">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="f459e-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="f459e-325">Uygulamayı çalıştırıp **Mvc Film** bağlantısını tıklarsanız, aşağıdakilere benzer bir hata alırsınız:</span><span class="sxs-lookup"><span data-stu-id="f459e-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-327">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="f459e-328">Veritabanını oluşturmanız gerekir ve bunu yapmak için EF Çekirdek [Geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="f459e-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="f459e-329">Geçişler, veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f459e-330">İlk geçiş</span><span class="sxs-lookup"><span data-stu-id="f459e-330">Initial migration</span></span>

<span data-ttu-id="f459e-331">Bu bölümde, aşağıdaki görevler tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="f459e-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="f459e-332">İlk geçiş ekleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-332">Add an initial migration.</span></span>
* <span data-ttu-id="f459e-333">Veritabanını ilk geçişle güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="f459e-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f459e-335">**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.</span><span class="sxs-lookup"><span data-stu-id="f459e-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="f459e-337">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="f459e-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="f459e-338">Komut, `Add-Migration` ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="f459e-339">Veritabanı şeması, sınıfta belirtilen modele `MvcMovieContext` dayanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="f459e-340">Bağımsız `Initial` değişken geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="f459e-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="f459e-341">Herhangi bir ad kullanılabilir, ancak kuralı yla, geçişi açıklayan bir ad kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="f459e-342">Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="f459e-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="f459e-343">Komut, `Update-Database` veritabanını `Up` oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.</span><span class="sxs-lookup"><span data-stu-id="f459e-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-344">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="f459e-345">Komut, `ef migrations add InitialCreate` ilk veritabanı şemasını oluşturmak için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="f459e-346">Veritabanı şeması `MvcMovieContext` sınıfta belirtilen modele dayanır *(Data/MvcMovieContext.cs* dosyasında).</span><span class="sxs-lookup"><span data-stu-id="f459e-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="f459e-347">Bağımsız `InitialCreate` değişken geçiş adıdır.</span><span class="sxs-lookup"><span data-stu-id="f459e-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f459e-348">Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f459e-349">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="f459e-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f459e-350">ASP.NET Core [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f459e-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f459e-351">Hizmetler (EF Core DB bağlamı gibi) uygulama nın başlatılması sırasında DI'ye kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="f459e-352">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f459e-353">DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f459e-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f459e-355">İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve di kapsayıcıile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="f459e-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="f459e-356">Aşağıdaki `Startup.ConfigureServices` yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f459e-357">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="f459e-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="f459e-358">Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `MvcMovieContext` koordinatları. `Movie`</span><span class="sxs-lookup"><span data-stu-id="f459e-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="f459e-359">Veri bağlamı`MvcMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="f459e-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f459e-360">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="f459e-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="f459e-361">Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f459e-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f459e-362">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f459e-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f459e-363">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f459e-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f459e-364">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f459e-365">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="f459e-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f459e-366">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f459e-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f459e-367">Bir DB bağlamı oluşturdunuz ve DI kapsayıcısı ile kaydettiniz.</span><span class="sxs-lookup"><span data-stu-id="f459e-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f459e-368">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="f459e-368">Test the app</span></span>

* <span data-ttu-id="f459e-369">Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).</span><span class="sxs-lookup"><span data-stu-id="f459e-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f459e-370">Aşağıdakilere benzer bir veritabanı özel durumu alırsanız:</span><span class="sxs-lookup"><span data-stu-id="f459e-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f459e-371">[Göçler adımını](#pmc)kaçırdın.</span><span class="sxs-lookup"><span data-stu-id="f459e-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f459e-372">**Oluştur** bağlantısını test edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-372">Test the **Create** link.</span></span> <span data-ttu-id="f459e-373">Veri girin ve gönderin.</span><span class="sxs-lookup"><span data-stu-id="f459e-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="f459e-374">`Price` Alana ondalık virgül giremeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f459e-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f459e-375">Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="f459e-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f459e-376">Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.</span><span class="sxs-lookup"><span data-stu-id="f459e-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f459e-377">**Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f459e-378">`Startup` Sınıfı inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="f459e-379">Önceki vurgulanan [kod, Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f459e-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="f459e-380">`services.AddDbContext<MvcMovieContext>(options =>`kullanılacak veritabanını ve bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f459e-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="f459e-381">`=>`bir [lambda operatörüdür](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="f459e-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="f459e-382">*Denetleyiciler/MoviesController.cs* dosyasını açın ve oluşturucuyu inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f459e-383">Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f459e-384">Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f459e-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="f459e-385">Güçlü bir şekilde yazılan @model modeller ve anahtar kelime</span><span class="sxs-lookup"><span data-stu-id="f459e-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="f459e-386">Bu öğreticinin başlarında, bir denetleyicinin `ViewData` sözlüğü kullanarak verileri veya nesneleri görünüme nasıl geçirebileceğini gördün.</span><span class="sxs-lookup"><span data-stu-id="f459e-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="f459e-387">Sözlük, `ViewData` bilgileri bir görünüme aktarmak için uygun bir geç bağlı yol sağlayan dinamik bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="f459e-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="f459e-388">MVC ayrıca güçlü bir şekilde yazılan model nesnelerini bir görünüme geçirme olanağı da sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="f459e-389">Bu güçlü bir şekilde yazılan yaklaşım, kodunuzu daha iyi derlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="f459e-390">İskele mekanizması, yöntem ve görünümleri oluşturduğunda `MoviesController` sınıf ve görünümlerle bu yaklaşımı (yani güçlü bir şekilde yazılan bir modeli geçirerek) kullanmıştır.</span><span class="sxs-lookup"><span data-stu-id="f459e-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="f459e-391">`Details` *Denetleyiciler/MoviesController.cs* dosyasında oluşturulan yöntemi inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="f459e-392">`id` Parametre genellikle rota verisi olarak geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="f459e-393">Örneğin `https://localhost:5001/movies/details/1` kümeler:</span><span class="sxs-lookup"><span data-stu-id="f459e-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="f459e-394">Denetleyiciye `movies` denetleyici (ilk URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="f459e-395">Eylem `details` (ikinci URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="f459e-396">Kimlik 1 (son URL segmenti).</span><span class="sxs-lookup"><span data-stu-id="f459e-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="f459e-397">Ayrıca aşağıdaki gibi `id` bir sorgu dize ile geçirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f459e-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="f459e-398">Bir `id` kimlik değeri sağlanmadığı durumlarda`int?`parametre , () geçersiz bir [tür](/dotnet/csharp/programming-guide/nullable-types/index) olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="f459e-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="f459e-399">Yol verileri veya sorgu `FirstOrDefaultAsync` dize değeriyle eşleşen film varlıklarını seçmek için [bir lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f459e-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="f459e-400">Bir film bulunursa, modelin `Movie` bir örneği `Details` görünüme aktarılır:</span><span class="sxs-lookup"><span data-stu-id="f459e-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="f459e-401">*Görünümler/Filmler/Ayrıntılar.cshtml* dosyasının içeriğini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="f459e-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="f459e-402">Görünüm dosyasının `@model` üst bölümüne bir deyim ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f459e-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="f459e-403">Film denetleyicisini oluşturduğunuzda, `@model` aşağıdaki ifade otomatik olarak *Details.cshtml* dosyasının en üstüne eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="f459e-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="f459e-404">Bu `@model` yönerge, denetleyicinin güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak görünüme geçtiği filme erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f459e-405">Örneğin, *Details.cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` güçlü bir şekilde yazılan `Model` nesneye sahip HTML Yardımcıları ve HTML Yardımcıları'na geçirir.</span><span class="sxs-lookup"><span data-stu-id="f459e-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="f459e-406">Ve yöntemler ve görünümler `Movie` de bir model nesnesi geçmek. `Edit` `Create`</span><span class="sxs-lookup"><span data-stu-id="f459e-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="f459e-407">Filmler denetleyicisinde *Index.cshtml* görünümünü ve `Index` yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="f459e-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="f459e-408">Kodun yöntemi aradığında `List` nesneyi nasıl `View` oluşturduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="f459e-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="f459e-409">Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:</span><span class="sxs-lookup"><span data-stu-id="f459e-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="f459e-410">Film denetleyicisini oluşturduğunuzda, iskele otomatik olarak `@model` *Index.cshtml* dosyasının üst kısmında aşağıdaki deyimi içerir:</span><span class="sxs-lookup"><span data-stu-id="f459e-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="f459e-411">Yönerge, `@model` güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak denetleyicinin görünüme geçtiği film listesine erişmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="f459e-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f459e-412">Örneğin, *Index.cshtml* görünümünde, kod filmler arasında güçlü `foreach` bir şekilde yazılan `Model` nesnenin üzerinde bir ifadeyle döngüler:</span><span class="sxs-lookup"><span data-stu-id="f459e-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="f459e-413">`Model` Nesne güçlü bir şekilde `IEnumerable<Movie>` (nesne olarak) yazıldığı için, döngüdeki her öğe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f459e-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="f459e-414">Diğer avantajların yanı sıra, bu kodun zaman denetimini derlediğiniz anlamına gelir:</span><span class="sxs-lookup"><span data-stu-id="f459e-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f459e-415">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f459e-415">Additional resources</span></span>

* [<span data-ttu-id="f459e-416">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="f459e-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="f459e-417">Genelleştirme ve yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="f459e-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="f459e-418">[Önceki](adding-view.md)
> [Veritabanıyla](working-with-sql.md) Sonraki Görünüm Ekleme</span><span class="sxs-lookup"><span data-stu-id="f459e-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
