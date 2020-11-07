Aşağıdaki .NET CLı komutlarını çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef -v 5.0.0-rc.2.20475.6
dotnet tool install --global dotnet-aspnet-codegenerator -v 5.0.0-rc.2.20516.1
dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
dotnet add package Microsoft.Extensions.Logging.Debug -v 5.0.0-*
```

Yukarıdaki komutlar şunları ekler:

* [ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* .NET CLı için EF Core araçları.
* EF Core paketini bir bağımlılık olarak yükleyecek EF Core SQLite sağlayıcı.
* Yapı iskelesi için gereken paketler: `Microsoft.VisualStudio.Web.CodeGeneration.Design` ve `Microsoft.EntityFrameworkCore.SqlServer` .

Bir uygulamanın, veritabanı bağlamlarını ortama göre yapılandırmasına izin veren birden çok ortam yapılandırmasında rehberlik için, bkz <xref:fundamentals/environments#environment-based-startup-class-and-methods> ..

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
