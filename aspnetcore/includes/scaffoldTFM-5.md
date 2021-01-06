<span data-ttu-id="bfbc9-101">Bir yapı iskelesi hatası alırsanız, hedef çerçeve adının (tfd) proje dosyasındaki NuGet paket sürümüyle eşleştiğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="bfbc9-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="bfbc9-102">Örneğin, aşağıdaki proje dosyası .NET için 5,0 sürümünü ve listelenen NuGet paketlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="bfbc9-102">For example, the following project file uses version 5.0 for .NET and the listed NuGet packages:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="5.0.0-*" />
  </ItemGroup>

</Project>
```
