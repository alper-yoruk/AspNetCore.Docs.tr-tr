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
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle ve ASP.NET Core ile başlayın

Bir Web API'sini tüketirken, çeşitli yöntemlerini anlamak bir geliştirici için zor olabilir. [OpenAPI](https://www.openapis.org/)olarak da bilinen [Swagger,](https://swagger.io/)Web API'leri için yararlı belgeler ve yardım sayfaları oluşturma sorununu çözer. Etkileşimli dokümantasyon, istemci SDK oluşturma ve API keşfedilebilirlik gibi avantajlar sağlar.

Bu örnekte, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) .NET uygulaması gösterilir.

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

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

`Startup.Configure` Yöntemde, oluşturulan JSON belgesive Swagger UI hizmet için ara yazılım etkinleştirin:

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

Önceki `UseSwaggerUI` yöntem arama statik [dosya middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)sağlar. .NET Framework veya .NET Core 1.x hedeflemesi durumunda, [projeye Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.

Uygulamayı başlatın ve `http://localhost:<port>/swagger/v1/swagger.json`'ye gidin. Uç noktaları açıklayan oluşturulan belge [Swagger belirtiminde (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)gösterildiği gibi görünür.

Swagger UI bulunabilir. `http://localhost:<port>/swagger` Swagger UI ile API'yi keşfedin ve diğer programlara dahil edin.

> [!TIP]
> Swagger UI'ye uygulamanın kökünden`http://localhost:<port>/`hizmet etmek `RoutePrefix` için ( ), özelliği boş bir dize olarak ayarlayın:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

IIS veya ters proxy ile dizinler kullanıyorsanız, `./` önek kullanarak Swagger bitiş noktasını göreli bir yola ayarlayın. Örneğin, `./swagger/v1/swagger.json`. Kullanmak, `/swagger/v1/swagger.json` uygulamanın URL'nin gerçek kökündeki JSON dosyasını aramasını (artı kullanılırsa rota öneki) kullanmatalimatı verir. Örneğin `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` yerine `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` kullanın.

## <a name="customize-and-extend"></a>Özelleştirin ve genişletin

Swagger, nesne modelini belgelemek ve kullanıcı arasını temanıza uyacak şekilde özelleştirmek için seçenekler sunar.

`Startup` Sınıfta, aşağıdaki ad alanlarını ekleyin:
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>API bilgileri ve açıklaması

`AddSwaggerGen` Yönteme geçirilen yapılandırma eylemi, yazar, lisans ve açıklama gibi bilgiler ekler:

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

Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı Özel Birimi: açıklama, yazar ve daha fazla bağlantı görün](sample_images/custom-info.png)

### <a name="xml-comments"></a>XML açıklamaları

XML yorumları aşağıdaki yaklaşımlarla etkinleştirilebilir:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın. **Araçlar** > **Dosyayı Edit'e**gidin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

XML yorumlarını etkinleştirmek, belgesiz genel türler ve üyeler için hata ayıklama bilgileri sağlar. Belgelenmemiş türleri ve üyeleri uyarı iletisi ile gösterilir. Örneğin, aşağıdaki ileti uyarı kodu 1591 ihlalini gösterir:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Proje genelinde ki uyarıları bastırmak için, proje dosyasında yoksayılması gereken yarı sütunlu sınırlı bir uyarı kodları listesi tanımlayın. [C# varsayılan](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) değerlerini `$(NoWarn);` de uygular için uyarı kodlarını ekler.

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

Yalnızca belirli üyeler için uyarıları bastırmak için, kodu uyarı önişlemci [yönergelerine #pragma](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) girin. Bu yaklaşım, API dokümanları aracılığıyla maruz kalmaması gereken kodlar için yararlıdır. Aşağıdaki örnekte, cs1591 uyarı kodu tüm `Program` sınıf için yoksayılır. Uyarı kodunun uygulanması sınıf tanımının sonunda geri yüklenir. Virgülle sınırlandırılmış listeyle birden çok uyarı kodu belirtin.

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

Swagger'ı, önceki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın. Linux veya Windows olmayan işletim sistemleri için dosya adları ve yollar büyük/küçük harf duyarlı olabilir. Örneğin, *Bir TodoApi.XML* dosyası Windows'da geçerlidir, ancak CentOS'ta geçerli değildir.

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

Önceki kodda, [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection) web API projesiyle eşleşen bir XML dosya adı oluşturmak için kullanılır. [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) özelliği XML dosyasına bir yol oluşturmak için kullanılır. Bazı Swagger özellikleri (örneğin, giriş parametrelerinin şema veya ilgili özniteliklerden HTTP yöntemleri ve yanıt kodları) bir XML dokümantasyon dosyası kullanmadan çalışır. Çoğu özellik, yani yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları için, bir XML dosyasının kullanılması zorunludur.

Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir. Eylemin üzerine [ \<bir özet>](/dotnet/csharp/programming-guide/xmldoc/summary) öğesi ekleyin: `Delete`

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
Swagger Kullanıcı İyiucu Su Birimi, önceki kodun `<summary>` öğesinin iç metnini görüntüler:

![XML yorumunu gösteren Swagger UI 'Belirli bir TodoItem'i siler.' DELETE yöntemi için](sample_images/triple-slash-comments.png)

UI oluşturulan JSON şema tarafından tahrik edilir:

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
Eylem yöntemi belgelerine [ \<bir açıklama>](/dotnet/csharp/programming-guide/xmldoc/remarks) öğesi ekleyin. `Create` Bu `<summary>` öğede belirtilen bilgileri tamamlar ve daha sağlam bir Swagger UI sağlar. `<remarks>` Öğe içeriği metin, JSON veya XML'den oluşabilir.

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
Bu ek açıklamalar ile UI geliştirmeleri dikkat edin:

![Gösterilen ek yorumlar ile Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Veri açıklamaları

Swagger UI bileşenlerini sürücüye yardımcı olmak için [modeli System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan özniteliklerle işaretleyin.

`[Required]` `Name` Sınıfın özelliğine öznitelik `TodoItem` ekleyin:

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

Bu öznitelik varlığı UI davranışını değiştirir ve altta yatan JSON şema sını değiştirir:

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

`[Produces("application/json")]` ÖZniteliği API denetleyicisine ekleyin. Amacı, denetleyicinin eylemlerinin bir yanıt içeriği *uygulama/json*türünü desteklediğini beyan etmektir:

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
**Yanıt İçerik Türü** açılır bu içerik türünü denetleyicinin GET eylemleri için varsayılan olarak seçer:

![Varsayılan yanıt içeriği türüne sahip Swagger Kullanıcı BiraSı](sample_images/json-response-content-type.png)

Web API'deki veri ek açıklamalarının kullanımı arttıkça, UI ve API yardım sayfaları daha açıklayıcı ve yararlı hale gelir.

### <a name="describe-response-types"></a>Yanıt türlerini açıklayın

Web API'si tüketen geliştiriciler en&mdash;çok yanıt türleri ve hata kodları (standart değilse) döndürülenlerle ilgilenirler. Yanıt türleri ve hata kodları XML yorumlarında ve veri ek açıklamalarında gösterilir.

Eylem `Create` başarı bir HTTP 201 durum kodu döndürür. Deftere nakledilen istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür. Swagger UI uygun belgeler olmadan, tüketici bu beklenen sonuçlar hakkında bilgi yoksundur. Aşağıdaki örnekte vurgulanan satırları ekleyerek bu sorunu giderin:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Swagger UI şimdi açıkça beklenen HTTP yanıt kodları belgeler:

![POSTA Yanıt Sınıfı açıklamasını gösteren Swagger UI 'Yeni oluşturulan Todo öğesini döndürür' ve '400 - Öğe null ise' durum kodu ve Yanıt İletileri altında neden için](sample_images/data-annotations-response-types.png)

Core 2.2 veya daha sonra ASP.NET, sözleşmeler açıkça ile bireysel eylemleri `[ProducesResponseType]`dekorasyon alternatif olarak kullanılabilir . Daha fazla bilgi için [bkz.](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)

Kullanıcı Arabirimi'ni özelleştirme hakkında daha fazla bilgi için bkz: [Kullanıcı Arabirimi'ni özelleştirin](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
