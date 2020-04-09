<a name="dc"></a>

### <a name="add-a-database-context-class"></a>Veritabanı bağlam sınıfı ekleme

RazorPagesMovie *projesinde, Veri*adlı yeni bir klasör oluşturun. Aşağıdaki `RazorPagesMovieContext` sınıfı *Veri* klasörüne ekleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir. Bağımlılıklar daha sonraki bir adımda eklenene kadar kod derlenmez.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi ekleme

Aşağıdaki vurgulanan kodda gösterildiği gibi *appsettings.json* dosyasına bir bağlantı dizesi ekleyin:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet paketleri ve EF araçları ekleyin

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-9)]

### <a name="add-required-nuget-packages"></a>Gerekli NuGet paketlerini ekleme

Projeye SQLite ve CodeGeneration.Design eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
```

Paket `Microsoft.VisualStudio.Web.CodeGeneration.Design` iskele için gereklidir.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Projeyi hatalar için bir denetim olarak oluşturun.

::: moniker-end
