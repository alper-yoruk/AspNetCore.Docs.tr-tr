<a name="dc"></a>

### <a name="add-a-database-context-class"></a><span data-ttu-id="2613f-101">Veritabanı bağlam sınıfı ekleme</span><span class="sxs-lookup"><span data-stu-id="2613f-101">Add a database context class</span></span>

<span data-ttu-id="2613f-102">RazorPagesMovie *projesinde, Veri*adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2613f-102">In the RazorPagesMovie project, create a new folder called *Data*.</span></span> <span data-ttu-id="2613f-103">Aşağıdaki `RazorPagesMovieContext` sınıfı *Veri* klasörüne ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2613f-103">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2613f-104">Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2613f-104">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2613f-105">Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="2613f-105">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2613f-106">Bağımlılıklar daha sonraki bir adımda eklenene kadar kod derlenmez.</span><span class="sxs-lookup"><span data-stu-id="2613f-106">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2613f-107">Veritabanı bağlantı dizesi ekleme</span><span class="sxs-lookup"><span data-stu-id="2613f-107">Add a database connection string</span></span>

<span data-ttu-id="2613f-108">Aşağıdaki vurgulanan kodda gösterildiği gibi *appsettings.json* dosyasına bir bağlantı dizesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2613f-108">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2613f-109">NuGet paketleri ve EF araçları ekleyin</span><span class="sxs-lookup"><span data-stu-id="2613f-109">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2613f-110">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="2613f-110">Register the database context</span></span>

<span data-ttu-id="2613f-111">Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2613f-111">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2613f-112">Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="2613f-112">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="2613f-113">Gerekli NuGet paketlerini ekleme</span><span class="sxs-lookup"><span data-stu-id="2613f-113">Add required NuGet packages</span></span>

<span data-ttu-id="2613f-114">Projeye SQLite ve CodeGeneration.Design eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="2613f-114">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

<span data-ttu-id="2613f-115">Paket `Microsoft.VisualStudio.Web.CodeGeneration.Design` iskele için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2613f-115">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2613f-116">Veritabanı bağlamını kaydetme</span><span class="sxs-lookup"><span data-stu-id="2613f-116">Register the database context</span></span>

<span data-ttu-id="2613f-117">Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2613f-117">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2613f-118">Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.</span><span class="sxs-lookup"><span data-stu-id="2613f-118">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="2613f-119">Projeyi hatalar için bir denetim olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="2613f-119">Build the project as a check for errors.</span></span>

::: moniker-end
