<span data-ttu-id="091f6-101">Aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="091f6-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="091f6-102">Önceki komutlar ekleyin:</span><span class="sxs-lookup"><span data-stu-id="091f6-102">The preceding commands add:</span></span>

* <span data-ttu-id="091f6-103">[Aspnet-codegenerator iskele aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="091f6-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="091f6-104">.NET Core CLI için Entity Framework Core Araçları.</span><span class="sxs-lookup"><span data-stu-id="091f6-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="091f6-105">EF Core paketini bağımlılık olarak yükleyen EF Core SQLite sağlayıcısı.</span><span class="sxs-lookup"><span data-stu-id="091f6-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="091f6-106">İskele için gerekli `Microsoft.VisualStudio.Web.CodeGeneration.Design` paketler: `Microsoft.EntityFrameworkCore.SqlServer`ve .</span><span class="sxs-lookup"><span data-stu-id="091f6-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="091f6-107">Bir uygulamanın veritabanı bağlamlarını ortama göre yapılandırmasına izin veren <xref:fundamentals/environments#environment-based-startup-class-and-methods>birden çok ortam yapılandırması hakkında kılavuz için bkz.</span><span class="sxs-lookup"><span data-stu-id="091f6-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]