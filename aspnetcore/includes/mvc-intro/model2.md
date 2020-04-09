::: moniker range=">= aspnetcore-3.0"

<a name="dc"></a>

Bir *Veri* klasörü oluşturun.

Aşağıdaki `MvcMovieContext` sınıfı *Veri* klasörüne ekleyin:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi ekleme

*appsettings.json* dosyasına bağlantı dizesi ekleyin:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet paketleri ve EF araçları ekleyin

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

Derleyici hataları için bir denetim olarak proje oluşturun.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`MvcMovieContext` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:  

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

Önceki kod varlık kümesi `DbSet` için bir özellik oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir ve varlık tablodaki bir satıra karşılık gelir.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi ekleme

*appsettings.json* dosyasına bağlantı dizesi ekleyin:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Gerekli NuGet paketlerini ekleme

Projeye SQLite ve CodeGeneration.Design eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
```

Paket `Microsoft.VisualStudio.Web.CodeGeneration.Design` iskele için gereklidir.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:

```csharp
using MvcMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısıyla `Startup.ConfigureServices`kaydedin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Projeyi hatalar için bir denetim olarak oluşturun.
::: moniker-end