Aşağıdaki .NET CLı komutlarını çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

Yukarıdaki komutlar şunları ekler:

* [ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* .NET CLı için EF Core araçları.
* EF Core paketini bir bağımlılık olarak yükleyecek EF Core SQLite sağlayıcı.
* Yapı iskelesi için gereken paketler: `Microsoft.VisualStudio.Web.CodeGeneration.Design` ve `Microsoft.EntityFrameworkCore.SqlServer` .

Bir uygulamanın, veritabanı bağlamlarını ortama göre yapılandırmasına izin veren birden çok ortam yapılandırmasında rehberlik için, bkz <xref:fundamentals/environments#environment-based-startup-class-and-methods> ..

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
