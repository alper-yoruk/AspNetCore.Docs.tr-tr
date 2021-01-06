<span data-ttu-id="c235a-101">Aşağıdaki .NET Core CLI komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c235a-101">Run the following .NET Core CLI commands:</span></span>

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

<span data-ttu-id="c235a-102">Yukarıdaki komutlar şunları ekler:</span><span class="sxs-lookup"><span data-stu-id="c235a-102">The preceding commands add:</span></span>

* <span data-ttu-id="c235a-103">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="c235a-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="c235a-104">.NET Core CLI için Entity Framework Core araçları.</span><span class="sxs-lookup"><span data-stu-id="c235a-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="c235a-105">EF Core paketini bir bağımlılık olarak yükleyecek EF Core SQLite sağlayıcı.</span><span class="sxs-lookup"><span data-stu-id="c235a-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="c235a-106">Yapı iskelesi için gereken paketler: `Microsoft.VisualStudio.Web.CodeGeneration.Design` ve `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="c235a-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="c235a-107">Bir uygulamanın, veritabanı bağlamlarını ortama göre yapılandırmasına izin veren birden çok ortam yapılandırmasında rehberlik için, bkz <xref:fundamentals/environments#environment-based-startup-class-and-methods> ..</span><span class="sxs-lookup"><span data-stu-id="c235a-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]