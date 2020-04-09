::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

<span data-ttu-id="b042e-101">Bir *Veri* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b042e-101">Create a *Data* folder.</span></span>

<span data-ttu-id="b042e-102">Aşağıdaki `MvcMovieContext` sınıfı *Veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-102">Add the following `MvcMovieContext` class to the *Data* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="b042e-103">Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b042e-103">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b042e-104">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="b042e-104">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="b042e-105">Veritabanı bağlantı dizesi ekleme</span><span class="sxs-lookup"><span data-stu-id="b042e-105">Add a database connection string</span></span>

<span data-ttu-id="b042e-106">*appsettings.json* dosyasına bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-106">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="b042e-107">NuGet paketleri ve EF araçları ekleyin</span><span class="sxs-lookup"><span data-stu-id="b042e-107">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b042e-108">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="b042e-108">Register the database context</span></span>

<span data-ttu-id="b042e-109">Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-109">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b042e-110">Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b042e-110">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

<span data-ttu-id="b042e-111">Derleyici hataları için bir denetim olarak proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b042e-111">Build the project as a check for compiler errors.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b042e-112">`MvcMovieContext` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-112">Add the following `MvcMovieContext` class to the *Models* folder:</span></span>  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="b042e-113">Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b042e-113">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="b042e-114">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="b042e-114">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="b042e-115">Veritabanı bağlantı dizesi ekleme</span><span class="sxs-lookup"><span data-stu-id="b042e-115">Add a database connection string</span></span>

<span data-ttu-id="b042e-116">*appsettings.json* dosyasına bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-116">Add a connection string to the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="b042e-117">Gerekli NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="b042e-117">Add required NuGet packages</span></span>

<span data-ttu-id="b042e-118">Projeye SQLite ve CodeGeneration.Design eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="b042e-118">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design  to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

<span data-ttu-id="b042e-119">Paket `Microsoft.VisualStudio.Web.CodeGeneration.Design` iskele için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="b042e-119">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="b042e-120">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="b042e-120">Register the database context</span></span>

<span data-ttu-id="b042e-121">Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b042e-121">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="b042e-122">Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b042e-122">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="b042e-123">Projeyi hatalar için bir denetim olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b042e-123">Build the project as a check for errors.</span></span>
::: moniker-end