---
title: NSwag ve ASP.NET Core ile başlayın
author: zuckerthoben
description: ASP.NET Core web API için belge ve yardım sayfaları oluşturmak için NSwag'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 3eae5d3c66204a10806a8036c8f114af6c501b2c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666056"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>NSwag ve ASP.NET Core ile başlayın

Christoph [Nienaber](https://twitter.com/zuckerthoben)tarafından , [Rico Suter](https://rsuter.com), ve [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag aşağıdaki yetenekleri sunar:

* Yeteneği Swagger UI ve Swagger jeneratör kullanmak için.
* Esnek kod oluşturma yetenekleri.

NSwag ile, Swagger'ı içeren&mdash;ve istemci uygulaması oluşturan üçüncü taraf API'leri kullanabileceğiniz varolan bir API'ye ihtiyacınız yoktur. NSwag geliştirme döngüsünü hızlandırmak ve kolayca API değişikliklere uyum sağlar.

## <a name="register-the-nswag-middleware"></a>NSwag ara yazılımını kaydedin

NSwag aracını şu şekilde kaydedin:

* Uygulanan web API'si için Swagger belirtimini oluşturun.
* Web API'sine göz atmak ve test etmek için Swagger UI'ye hizmet edin.

[NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core ara yazılımını kullanmak için [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet paketini yükleyin. Bu paket oluşturmak ve Swagger belirtimi, Swagger UI (v2 ve v3) ve [ReDoc UI](https://github.com/Rebilly/ReDoc)hizmet ara içerir.

NSwag NuGet paketini yüklemek için aşağıdaki yaklaşımlardan birini kullanın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Paket **Yöneticisi Konsolu** penceresinden:
  * **Diğer Windows** > **Paket Yöneticisi Konsolu'na** **Git** > 
  * *TodoApi.csproj* dosyasının bulunduğu dizine gidin
  * Aşağıdaki komutu yürütün:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* **NuGet Paketlerini Yönet** iletişim kutusundan:
  * **Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın
  * Paket **kaynağını** "nuget.org" olarak ayarlama
  * Arama kutusuna "NSwag.AspNetCore" girin
  * **Gözat** sekmesinden "NSwag.AspNetCore" paketini seçin ve **Yükle'yi** tıklatın

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm Pad** > Ekle Paketleri'ndeki *Paketler* klasörüne sağ**tıklayın...**
* Paketleri **Ekle** penceresinin **Kaynak** açılır penceresini "nuget.org" olarak ayarlama
* Arama kutusuna "NSwag.AspNetCore" girin
* Sonuç bölmesinden "NSwag.AspNetCore" paketini seçin ve **Paket Ekle'yi** tıklatın

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

Aşağıdaki adımları gerçekleştirerek ASP.NET Core uygulamanızda Swagger ekleyin ve yapılandırın:

* `Startup.ConfigureServices` Yöntemde, gerekli Swagger hizmetlerini kaydedin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* `Startup.Configure` Yöntemde, oluşturulan Swagger belirtimi ve Swagger UI hizmet için ara yazılım etkinleştirin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Uygulamayı başlatma. Şuna gidin:
  * `http://localhost:<port>/swagger`Swagger UI görüntülemek için.
  * `http://localhost:<port>/swagger/v1/swagger.json`Swagger belirtimini görüntülemek için.

## <a name="code-generation"></a>Kod oluşturma

Aşağıdaki seçeneklerden birini seçerek NSwag'ın kod oluşturma özelliklerinden yararlanabilirsiniz:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; C# veya TypeScript API istemci kodu oluşturmak için bir Windows masaüstü uygulaması.
* Projeniz içinde kod oluşturma için [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) veya [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet paketleri.
* [Komuta satırından](https://github.com/RicoSuter/NSwag/wiki/CommandLine)NSwag.
* [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet paketi.
* [Unchase OpenAPI (Swagger) Bağlı Hizmet](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; C# veya TypeScript API istemci kodu oluşturmak için bir Visual Studio Connected Service. Ayrıca NSwag ile OpenAPI hizmetleri için C# denetleyicileri oluşturur.

### <a name="generate-code-with-nswagstudio"></a>NSwagStudio ile kod oluşturma

* [NSwagStudio GitHub deposunda talimatları izleyerek NSwagStudio yükleyin.](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) NSwag yayın sayfasında yükleme ve yönetici ayrıcalıkları olmadan başlatılabilir bir xcopy sürümünü indirebilirsiniz.
* NSwagStudio'yu başlatın ve **Swagger Specification URL** metin kutusuna *swagger.json* dosya URL'sini girin. Örneğin, *http://localhost:44354/swagger/v1/swagger.json*.
* Swagger belirtiminizin JSON temsilini oluşturmak için **yerel Kopya oluştur** düğmesini tıklatın.

  ![Swagger belirtiminin yerel kopyasını oluşturma](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* **Çıktılar** alanında **CSharp İstemci** onay kutusunu tıklatın. Projenize bağlı olarak **TypeScript Client** veya **CSharp Web API Denetleyicisi'ni**de seçebilirsiniz. **CSharp Web API Denetleyicisi'ni**seçerseniz, bir hizmet belirtimi ters nesil olarak hizmet veren hizmeti yeniden belirler.
* *TodoApi.NSwag* projesinin tam bir C# istemci seçimi üretmek için **ürünleri Oluştur'** u tıklatın. Oluşturulan istemci kodunu görmek için **CSharp İstemci** sekmesini tıklatın:

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> C# istemci **kodu, Ayarlar** sekmesindeki seçimlere göre oluşturulur.

* Oluşturulan C# kodunu istemci projesinde API'yi tüketecek bir dosyaya kopyalayın.
* Web API'sini tüketmeye başlayın:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>API belgelerini özelleştirme

Swagger, web API tüketimini kolaylaştırmak için nesne modelini belgelemek için seçenekler sunar.

### <a name="api-info-and-description"></a>API bilgileri ve açıklaması

`Startup.ConfigureServices` Yöntemde, `AddSwaggerDocument` yönteme geçirilen bir yapılandırma eylemi yazar, lisans ve açıklama gibi bilgiler ekler:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı Bira](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML açıklamaları

XML açıklamalarını etkinleştirmek için aşağıdaki adımları gerçekleştirin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Solution Explorer'da** projeye sağ tıklayın ve **Özellikler'i** seçin
* **Yapı** sekmesinin **Çıktı** bölümü altındaki **XML belgedosya** kutusunu denetleme

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın. **Araçlar** > **Dosyayı Edit'e**gidin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* > **Yapı** **Derleyicisi** > **Proje Seçenekleri** iletişim kutusunu açın
* **Genel Seçenekler** bölümünün altındaki **XML dokümantasyon** kutusunu oluştur'u işaretleyin

::: moniker-end

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Veri açıklamaları

::: moniker range="<= aspnetcore-2.0"

NSwag [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullanır ve web API eylemleri için önerilen dönüş türü [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult)olduğundan, eyleminizi ne yaptığını ve ne döndürür çıkaramaz.

Aşağıdaki örneği inceleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Önceki eylem döndürür, `IActionResult`ancak eylem içinde [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) veya [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)döndürür. İstemcilere bu eylemin hangi HTTP durum kodlarını döndüreceğini söylemek için veri ek açıklamalarını kullanın. Eylemi aşağıdaki özniteliklerle işaretleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

NSwag [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullanır ve web API eylemleri için önerilen dönüş türü [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)olduğundan, yalnızca tarafından `T`tanımlanan dönüş türü çıkarabilirsiniz . Diğer olası iade türlerini otomatik olarak çıkaramazsınız.

Aşağıdaki örneği inceleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Önceki eylem döndürür. `ActionResult<T>` Eylem içinde, [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*)döndürediyor. Denetleyici özniteliğe [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) sahip olduğundan, bir [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) yanıtı da mümkündür. Daha fazla bilgi için [Otomatik HTTP 400 yanıtları'na](xref:web-api/index#automatic-http-400-responses)bakın. İstemcilere bu eylemin hangi HTTP durum kodlarını döndüreceğini söylemek için veri ek açıklamalarını kullanın. Eylemi aşağıdaki özniteliklerle işaretleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

Core 2.2 veya daha sonra ASP.NET, tek eylemleri açıkça .' `[ProducesResponseType]`ile süslemek yerine konvansiyonları kullanabilirsiniz. Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.

::: moniker-end

Swagger jeneratörü artık bu eylemi doğru bir şekilde açıklayabilir ve oluşturulan istemciler bitiş noktasını ararken ne aldıklarını bilirler. Bir öneri olarak, tüm eylemleri bu özniteliklerle işaretleyin.

API eylemlerinizin ne kadar HTTP yanıtları döndürmesi gerektiğine ilişkin yönergeler için [RFC 7231 belirtimine](https://tools.ietf.org/html/rfc7231#section-4.3)bakın.
