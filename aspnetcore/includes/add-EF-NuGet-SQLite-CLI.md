Aşağıdaki .NET Core CLI komutları çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

Yukarıdaki komutlar şunları ekler:

* [ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* .NET Core CLI için Entity Framework Core araçları.
* EF Core paketini bir bağımlılık olarak yükleyecek EF Core SQLite sağlayıcı.
* Yapı iskelesi için gereken paketler: `Microsoft.VisualStudio.Web.CodeGeneration.Design` ve `Microsoft.EntityFrameworkCore.SqlServer` .

Bir uygulamanın, veritabanı bağlamlarını ortama göre yapılandırmasına izin veren birden çok ortam yapılandırmasında rehberlik için, bkz <xref:fundamentals/environments#environment-based-startup-class-and-methods> ..

[!INCLUDE[](~/includes/scaffoldTFM.md)]