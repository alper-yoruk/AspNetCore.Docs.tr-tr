Aşağıdaki .NET Core CLI komutlarını çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Önceki komutlar ekleyin:

* [Aspnet-codegenerator iskele aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* .NET Core CLI için Entity Framework Core Araçları.
* EF Core paketini bağımlılık olarak yükleyen EF Core SQLite sağlayıcısı.
* İskele için gerekli `Microsoft.VisualStudio.Web.CodeGeneration.Design` paketler: `Microsoft.EntityFrameworkCore.SqlServer`ve .

Bir uygulamanın veritabanı bağlamlarını ortama göre yapılandırmasına izin veren <xref:fundamentals/environments#environment-based-startup-class-and-methods>birden çok ortam yapılandırması hakkında kılavuz için bkz.

[!INCLUDE[](~/includes/scaffoldTFM.md)]