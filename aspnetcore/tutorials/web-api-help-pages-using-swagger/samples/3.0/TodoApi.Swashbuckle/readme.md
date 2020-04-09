---
page_type: sample
description: Swagger UI'yi entegre etmek için ASP.NET Core web API projenize Swashbuckle'ı nasıl ekleyeceğinizi öğrenin.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659938"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="910c6-102">Swashbuckle ve ASP.NET Core ile başlayın</span><span class="sxs-lookup"><span data-stu-id="910c6-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="910c6-103">Bir Web API'sini tüketirken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="910c6-104">[OpenAPI](https://www.openapis.org/)olarak da bilinen [Swagger,](https://swagger.io/)Web API'leri için yararlı belgeler ve yardım sayfaları oluşturma sorununu çözer.</span><span class="sxs-lookup"><span data-stu-id="910c6-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="910c6-105">Etkileşimli dokümantasyon, istemci SDK oluşturma ve API keşfedilebilirlik gibi avantajlar sağlar.</span><span class="sxs-lookup"><span data-stu-id="910c6-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="910c6-106">Bu örnekte, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) .NET uygulaması gösterilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="910c6-107">Swagger ara yazılım ekleme ve yapılandırma</span><span class="sxs-lookup"><span data-stu-id="910c6-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="910c6-108">`Startup.Configure` Yöntemde, oluşturulan JSON belgesive Swagger UI hizmet için ara yazılım etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="910c6-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="910c6-109">Önceki `UseSwaggerUI` yöntem arama statik [dosya middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)sağlar.</span><span class="sxs-lookup"><span data-stu-id="910c6-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="910c6-110">.NET Framework veya .NET Core 1.x hedeflemesi durumunda, [projeye Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="910c6-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="910c6-111">Uygulamayı başlatın ve `http://localhost:<port>/swagger/v1/swagger.json`'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="910c6-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="910c6-112">Uç noktaları açıklayan oluşturulan belge [Swagger belirtiminde (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)gösterildiği gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="910c6-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="910c6-113">Swagger UI bulunabilir. `http://localhost:<port>/swagger`</span><span class="sxs-lookup"><span data-stu-id="910c6-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="910c6-114">Swagger UI ile API'yi keşfedin ve diğer programlara dahil edin.</span><span class="sxs-lookup"><span data-stu-id="910c6-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="910c6-115">Swagger UI'ye uygulamanın kökünden`http://localhost:<port>/`hizmet etmek `RoutePrefix` için ( ), özelliği boş bir dize olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="910c6-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="910c6-116">IIS veya ters proxy ile dizinler kullanıyorsanız, `./` önek kullanarak Swagger bitiş noktasını göreli bir yola ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="910c6-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="910c6-117">Örneğin, `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="910c6-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="910c6-118">Kullanmak, `/swagger/v1/swagger.json` uygulamanın URL'nin gerçek kökündeki JSON dosyasını aramasını (artı kullanılırsa rota öneki) kullanmatalimatı verir.</span><span class="sxs-lookup"><span data-stu-id="910c6-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="910c6-119">Örneğin `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` yerine `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` kullanın.</span><span class="sxs-lookup"><span data-stu-id="910c6-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="910c6-120">Özelleştirin ve genişletin</span><span class="sxs-lookup"><span data-stu-id="910c6-120">Customize and extend</span></span>

<span data-ttu-id="910c6-121">Swagger, nesne modelini belgelemek ve kullanıcı arasını temanıza uyacak şekilde özelleştirmek için seçenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="910c6-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="910c6-122">`Startup` Sınıfta, aşağıdaki ad alanlarını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="910c6-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="910c6-123">API bilgileri ve açıklaması</span><span class="sxs-lookup"><span data-stu-id="910c6-123">API info and description</span></span>

<span data-ttu-id="910c6-124">`AddSwaggerGen` Yönteme geçirilen yapılandırma eylemi, yazar, lisans ve açıklama gibi bilgiler ekler:</span><span class="sxs-lookup"><span data-stu-id="910c6-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="910c6-125">Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="910c6-125">The Swagger UI displays the version's information:</span></span>

![Sürüm bilgileriyle Swagger Kullanıcı Özel Birimi: açıklama, yazar ve daha fazla bağlantı görün](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="910c6-127">XML açıklamaları</span><span class="sxs-lookup"><span data-stu-id="910c6-127">XML comments</span></span>

<span data-ttu-id="910c6-128">XML yorumları aşağıdaki yaklaşımlarla etkinleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="910c6-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="910c6-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="910c6-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="910c6-130">**Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.</span><span class="sxs-lookup"><span data-stu-id="910c6-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="910c6-131">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="910c6-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="910c6-132">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="910c6-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="910c6-133">Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="910c6-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="910c6-134">**Araçlar** > **Dosyayı Edit'e**gidin.</span><span class="sxs-lookup"><span data-stu-id="910c6-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="910c6-135">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="910c6-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="910c6-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="910c6-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="910c6-137">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="910c6-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="910c6-138">XML yorumlarını etkinleştirmek, belgesiz genel türler ve üyeler için hata ayıklama bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="910c6-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="910c6-139">Belgelenmemiş türleri ve üyeleri uyarı iletisi ile gösterilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="910c6-140">Örneğin, aşağıdaki ileti uyarı kodu 1591 ihlalini gösterir:</span><span class="sxs-lookup"><span data-stu-id="910c6-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="910c6-141">Proje genelinde ki uyarıları bastırmak için, proje dosyasında yoksayılması gereken yarı sütunlu sınırlı bir uyarı kodları listesi tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="910c6-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="910c6-142">[C# varsayılan](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) değerlerini `$(NoWarn);` de uygular için uyarı kodlarını ekler.</span><span class="sxs-lookup"><span data-stu-id="910c6-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="910c6-143">Yalnızca belirli üyeler için uyarıları bastırmak için, kodu uyarı önişlemci [yönergelerine #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) girin.</span><span class="sxs-lookup"><span data-stu-id="910c6-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="910c6-144">Bu yaklaşım, API dokümanları aracılığıyla maruz kalmaması gereken kodlar için yararlıdır. Aşağıdaki örnekte, cs1591 uyarı kodu tüm `Program` sınıf için yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="910c6-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="910c6-145">Uyarı kodunun uygulanması sınıf tanımının sonunda geri yüklenir.</span><span class="sxs-lookup"><span data-stu-id="910c6-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="910c6-146">Virgülle sınırlandırılmış listeyle birden çok uyarı kodu belirtin.</span><span class="sxs-lookup"><span data-stu-id="910c6-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="910c6-147">Swagger'ı, önceki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="910c6-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="910c6-148">Linux veya Windows olmayan işletim sistemleri için dosya adları ve yollar büyük/küçük harf duyarlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="910c6-149">Örneğin, *Bir TodoApi.XML* dosyası Windows'da geçerlidir, ancak CentOS'ta geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="910c6-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="910c6-150">Önceki kodda, [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection) web API projesiyle eşleşen bir XML dosya adı oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="910c6-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="910c6-151">[AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) özelliği XML dosyasına bir yol oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="910c6-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="910c6-152">Bazı Swagger özellikleri (örneğin, giriş parametrelerinin şema veya ilgili özniteliklerden HTTP yöntemleri ve yanıt kodları) bir XML dokümantasyon dosyası kullanmadan çalışır.</span><span class="sxs-lookup"><span data-stu-id="910c6-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="910c6-153">Çoğu özellik, yani yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları için, bir XML dosyasının kullanılması zorunludur.</span><span class="sxs-lookup"><span data-stu-id="910c6-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="910c6-154">Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="910c6-155">Eylemin üzerine [ \<bir özet>](/dotnet/csharp/programming-guide/xmldoc/summary) öğesi ekleyin: `Delete`</span><span class="sxs-lookup"><span data-stu-id="910c6-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="910c6-156">Swagger Kullanıcı İyiucu Su Birimi, önceki kodun `<summary>` öğesinin iç metnini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="910c6-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![XML yorumunu gösteren Swagger UI 'Belirli bir TodoItem'i siler.'](sample_images/triple-slash-comments.png)

<span data-ttu-id="910c6-159">UI oluşturulan JSON şema tarafından tahrik edilir:</span><span class="sxs-lookup"><span data-stu-id="910c6-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="910c6-160">Eylem yöntemi belgelerine [ \<bir açıklama>](/dotnet/csharp/programming-guide/xmldoc/remarks) öğesi ekleyin. `Create`</span><span class="sxs-lookup"><span data-stu-id="910c6-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="910c6-161">Bu `<summary>` öğede belirtilen bilgileri tamamlar ve daha sağlam bir Swagger UI sağlar.</span><span class="sxs-lookup"><span data-stu-id="910c6-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="910c6-162">`<remarks>` Öğe içeriği metin, JSON veya XML'den oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="910c6-163">Bu ek açıklamalar ile UI geliştirmeleri dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="910c6-163">Notice the UI enhancements with these additional comments:</span></span>

![Gösterilen ek yorumlar ile Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="910c6-165">Veri açıklamaları</span><span class="sxs-lookup"><span data-stu-id="910c6-165">Data annotations</span></span>

<span data-ttu-id="910c6-166">Swagger UI bileşenlerini sürücüye yardımcı olmak için [modeli System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan özniteliklerle işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="910c6-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="910c6-167">`[Required]` `Name` Sınıfın özelliğine öznitelik `TodoItem` ekleyin:</span><span class="sxs-lookup"><span data-stu-id="910c6-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="910c6-168">Bu öznitelik varlığı UI davranışını değiştirir ve altta yatan JSON şema sını değiştirir:</span><span class="sxs-lookup"><span data-stu-id="910c6-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="910c6-169">`[Produces("application/json")]` ÖZniteliği API denetleyicisine ekleyin.</span><span class="sxs-lookup"><span data-stu-id="910c6-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="910c6-170">Amacı, denetleyicinin eylemlerinin bir yanıt içeriği *uygulama/json*türünü desteklediğini beyan etmektir:</span><span class="sxs-lookup"><span data-stu-id="910c6-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="910c6-171">**Yanıt İçerik Türü** açılır bu içerik türünü denetleyicinin GET eylemleri için varsayılan olarak seçer:</span><span class="sxs-lookup"><span data-stu-id="910c6-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Varsayılan yanıt içeriği türüne sahip Swagger Kullanıcı BiraSı](sample_images/json-response-content-type.png)

<span data-ttu-id="910c6-173">Web API'deki veri ek açıklamalarının kullanımı arttıkça, UI ve API yardım sayfaları daha açıklayıcı ve yararlı hale gelir.</span><span class="sxs-lookup"><span data-stu-id="910c6-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="910c6-174">Yanıt türlerini açıklayın</span><span class="sxs-lookup"><span data-stu-id="910c6-174">Describe response types</span></span>

<span data-ttu-id="910c6-175">Web API'si tüketen geliştiriciler en&mdash;çok yanıt türleri ve hata kodları (standart değilse) döndürülenlerle ilgilenirler.</span><span class="sxs-lookup"><span data-stu-id="910c6-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="910c6-176">Yanıt türleri ve hata kodları XML yorumlarında ve veri ek açıklamalarında gösterilir.</span><span class="sxs-lookup"><span data-stu-id="910c6-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="910c6-177">Eylem `Create` başarı bir HTTP 201 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="910c6-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="910c6-178">Deftere nakledilen istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="910c6-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="910c6-179">Swagger UI uygun belgeler olmadan, tüketici bu beklenen sonuçlar hakkında bilgi yoksundur.</span><span class="sxs-lookup"><span data-stu-id="910c6-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="910c6-180">Aşağıdaki örnekte vurgulanan satırları ekleyerek bu sorunu giderin:</span><span class="sxs-lookup"><span data-stu-id="910c6-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="910c6-181">Swagger UI şimdi açıkça beklenen HTTP yanıt kodları belgeler:</span><span class="sxs-lookup"><span data-stu-id="910c6-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POSTA Yanıt Sınıfı açıklamasını gösteren Swagger UI 'Yeni oluşturulan Todo öğesini döndürür' ve '400 - Öğe null ise' durum kodu ve Yanıt İletileri altında neden için](sample_images/data-annotations-response-types.png)

<span data-ttu-id="910c6-183">Core 2.2 veya daha sonra ASP.NET, sözleşmeler açıkça ile bireysel eylemleri `[ProducesResponseType]`dekorasyon alternatif olarak kullanılabilir .</span><span class="sxs-lookup"><span data-stu-id="910c6-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="910c6-184">Daha fazla bilgi için [bkz.](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)</span><span class="sxs-lookup"><span data-stu-id="910c6-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="910c6-185">Kullanıcı Arabirimi'ni özelleştirme hakkında daha fazla bilgi için bkz: [Kullanıcı Arabirimi'ni özelleştirin](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="910c6-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
