<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="f69d0-101">NuGet paketleri ve EF araçları ekleme</span><span class="sxs-lookup"><span data-stu-id="f69d0-101">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="f69d0-102">Veritabanı bağlamı sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="f69d0-102">Add a database context class</span></span>

<span data-ttu-id="f69d0-103">RazorPagesMovie projesinde, *veri*adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f69d0-103">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="f69d0-104">Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f69d0-104">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="f69d0-105">Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f69d0-105">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="f69d0-106">Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="f69d0-106">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="f69d0-107">Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="f69d0-107">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="f69d0-108">Veritabanı bağlantı dizesi Ekle</span><span class="sxs-lookup"><span data-stu-id="f69d0-108">Add a database connection string</span></span>

<span data-ttu-id="f69d0-109">Aşağıdaki Vurgulanan kodda gösterildiği gibi *appSettings. JSON* dosyasına bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f69d0-109">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f69d0-110">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="f69d0-110">Register the database context</span></span>

<span data-ttu-id="f69d0-111">Aşağıdaki `using` deyimlerini *Startup.cs*üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f69d0-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f69d0-112">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f69d0-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="f69d0-113">Gerekli NuGet paketlerini ekleyin</span><span class="sxs-lookup"><span data-stu-id="f69d0-113">Add required NuGet packages</span></span>

<span data-ttu-id="f69d0-114">SQLite ve CodeGeneration eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın. projeye tasarım:</span><span class="sxs-lookup"><span data-stu-id="f69d0-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="f69d0-115">`Microsoft.VisualStudio.Web.CodeGeneration.Design`Paket, yapı iskelesi için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f69d0-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="f69d0-116">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="f69d0-116">Register the database context</span></span>

<span data-ttu-id="f69d0-117">Aşağıdaki `using` deyimlerini *Startup.cs*üst kısmına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f69d0-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f69d0-118">Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="f69d0-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="f69d0-119">Hataları denetlemek için projeyi bir denetim olarak derleyin.</span><span class="sxs-lookup"><span data-stu-id="f69d0-119">Build the project as a check for errors.</span></span>

::: moniker-end
