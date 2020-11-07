<span data-ttu-id="e7f9a-101">Aşağıdaki .NET CLı komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e7f9a-101">Run the following .NET CLI commands:</span></span>

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

<span data-ttu-id="e7f9a-102">Yukarıdaki komutlar şunları ekler:</span><span class="sxs-lookup"><span data-stu-id="e7f9a-102">The preceding commands add:</span></span>

* <span data-ttu-id="e7f9a-103">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="e7f9a-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="e7f9a-104">.NET CLı için EF Core araçları.</span><span class="sxs-lookup"><span data-stu-id="e7f9a-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="e7f9a-105">EF Core paketini bir bağımlılık olarak yükleyecek EF Core SQLite sağlayıcı.</span><span class="sxs-lookup"><span data-stu-id="e7f9a-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="e7f9a-106">Yapı iskelesi için gereken paketler: `Microsoft.VisualStudio.Web.CodeGeneration.Design` ve `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="e7f9a-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="e7f9a-107">Bir uygulamanın, veritabanı bağlamlarını ortama göre yapılandırmasına izin veren birden çok ortam yapılandırmasında rehberlik için, bkz <xref:fundamentals/environments#environment-based-startup-class-and-methods> ..</span><span class="sxs-lookup"><span data-stu-id="e7f9a-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
