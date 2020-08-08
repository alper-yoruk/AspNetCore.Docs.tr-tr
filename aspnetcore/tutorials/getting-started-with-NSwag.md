---
title: NSwag ve ASP.NET Core kullanmaya başlayın
author: zuckerthoben
description: ASP.NET Core Web API 'SI için belge ve yardım sayfaları oluşturmak üzere NSwag 'yi nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8fd42c7d31edd20c2aae7577c5a490b54ab8129c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022270"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>NSwag ve ASP.NET Core kullanmaya başlayın

, [Christoph Nienaber](https://twitter.com/zuckerthoben), [Riko Suter](https://rsuter.com)ve [bave Brock](https://twitter.com/daveabrock) tarafından

::: moniker range=">= aspnetcore-2.1"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag aşağıdaki özellikleri sunar:

* Swagger Kullanıcı arabirimi ve Swagger oluşturucuyu kullanma özelliği.
* Esnek kod oluşturma özellikleri.

NSwag ile, var olan bir API 'ye gerek kalmaz &mdash; Swagger içeren üçüncü taraf API 'leri kullanabilir ve bir istemci uygulamasını oluşturabilirsiniz. NSwag, geliştirme döngüsünü hızlandırın ve API değişikliklerine kolayca uyum sağlar.

## <a name="register-the-nswag-middleware"></a>NSwag ara yazılımını kaydetme

NSwag ara yazılımını şu şekilde kaydedin:

* Uygulanan Web API 'SI için Swagger belirtimini oluşturun.
* Web API 'sini taramak ve test etmek için Swagger Kullanıcı arabirimini sunar.

[Nswag](https://github.com/RicoSuter/NSwag) ASP.NET Core ara yazılımını kullanmak Için [nswag. aspnetcore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet paketini yüklemelisiniz. Bu paket Swagger belirtimini oluşturma ve sunma ara yazılımını, Swagger Kullanıcı arabirimini (v2 ve v3) ve [Redoc Kullanıcı arabirimini](https://github.com/Rebilly/ReDoc)içerir.

NSwag NuGet paketini yüklemek için aşağıdaki yaklaşımlardan birini kullanın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsol** penceresinde:
  * **View**  >  **Diğer Windows**  >  **Paket Yöneticisi konsolunu** görüntüle ' ye git
  * *TodoApi. csproj* dosyasının bulunduğu dizine gidin
  * Aşağıdaki komutu yürütün:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* **NuGet Paketlerini Yönet** iletişim kutusunda:
  * **Çözüm Gezgini**  >  **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın
  * **Paket kaynağını** "NuGet.org" olarak ayarlayın
  * Arama kutusuna "NSwag. AspNetCore" yazın
  * **Araştır** sekmesinden "NSwag. aspnetcore" paketini seçin ve sonra da **yüklensin** ' e tıklayın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Packages* **Çözüm bölmesi**  >  **paket Ekle...** ' da paketler klasörüne sağ tıklayın.
* **Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın
* Arama kutusuna "NSwag. AspNetCore" yazın
* Sonuçlar bölmesinden "NSwag. AspNetCore" paketini seçin ve **paket Ekle** ' ye tıklayın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

Aşağıdaki adımları gerçekleştirerek ASP.NET Core uygulamanızda Swagger ekleyin ve yapılandırın:

* `Startup.ConfigureServices`Yönteminde, gerekli Swagger hizmetlerini kaydedin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* `Startup.Configure`Yönteminde, oluşturulan Swagger belirtimini ve Swagger Kullanıcı arabirimini sunan ara yazılımı etkinleştirin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Uygulamayı başlatma. Şuraya gidin:
  * `http://localhost:<port>/swagger`Swagger Kullanıcı arabirimini görüntülemek için.
  * `http://localhost:<port>/swagger/v1/swagger.json`Swagger belirtimini görüntülemek için.

## <a name="code-generation"></a>Kod oluşturma

Aşağıdaki seçeneklerden birini seçerek NSwag 'nin kod oluşturma özelliğinden yararlanabilirsiniz:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): C# veya TYPESCRIPT 'te API istemci kodu oluşturmaya yönelik bir Windows masaüstü uygulaması.
* Projenizin içindeki kod üretimi için [Nswag. CodeGeneration. CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) veya [Nswag. CodeGeneration. TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet paketleri.
* [Komut satırından](https://github.com/RicoSuter/NSwag/wiki/CommandLine)NSwag.
* [Nswag. MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet paketi.
* [Unchase Openapı (Swagger) bağlı hizmeti](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): C# veya TYPESCRIPT 'te API istemci kodu oluşturmak Için bir Visual Studio bağlı hizmeti. Ayrıca, NSwag ile Openapı Hizmetleri için C# denetleyicileri oluşturur.

### <a name="generate-code-with-nswagstudio"></a>NSwagStudio ile kod oluşturma

* [NSwagStudio GitHub deposundaki](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)yönergeleri izleyerek NSwagStudio 'i yükler. NSwag Release sayfasında, yükleme ve yönetici ayrıcalıkları olmadan başlatılabilen bir Xcopy sürümü indirebilirsiniz.
* NSwagStudio başlatın ve **Swagger belirtim URL 'si** metin kutusunda dosya URL *'sindekiswagger.js* girin. Örneğin, *http://localhost:44354/swagger/v1/swagger.json*.
* Swagger belirtimin bir JSON gösterimini oluşturmak için **Yerel kopya oluştur** düğmesine tıklayın.

  ![Swagger belirtiminin yerel kopyasını oluştur](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* **Çıktılar** alanında, **CSharp Client** onay kutusuna tıklayın. Projenize bağlı olarak, **TypeScript Client** veya **CSharp Web API Controller**' ı da seçebilirsiniz. **CSharp Web API denetleyicisi**' ni seçerseniz, bir hizmet belirtimi hizmeti yeniden oluşturur ve bu da ters bir oluşturma görevi görür.
* *TodoApi. NSwag* projesinin tüm C# istemci uygulamasını oluşturmak Için **çıkış oluştur** ' a tıklayın. Oluşturulan istemci kodunu görmek için **CSharp istemci** sekmesine tıklayın:

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
> C# istemci kodu, **Ayarlar** sekmesindeki seçimlere göre oluşturulur. varsayılan ad alanı yeniden adlandırma ve zaman uyumlu yöntem oluşturma gibi görevleri gerçekleştirmek Için ayarları değiştirin.

* Oluşturulan C# kodunu, API 'YI kullanacak istemci projesindeki bir dosyaya kopyalayın.
* Web API 'sini kullanmayı Başlat:

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

Swagger, Web API 'sinin kullanımını kolaylaştırmak için nesne modelini belgeleme seçeneklerini sağlar.

### <a name="api-info-and-description"></a>API bilgisi ve açıklaması

Yönteminde, `Startup.ConfigureServices` yöntemine geçirilen bir yapılandırma eylemi `AddSwaggerDocument` Yazar, lisans ve açıklama gibi bilgileri ekler:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Swagger Kullanıcı arabirimi, sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML açıklamaları

XML açıklamalarını etkinleştirmek için aşağıdaki adımları uygulayın:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **Çözüm Gezgini** projeye sağ tıklayın ve **>. csproj Project_Name <Düzenle**' yi seçin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Özellikler** ' i seçin
* **Build** sekmesinin **output** bölümünün altındaki **XML belge dosyası** kutusunu işaretleyin

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* *Çözüm bölmesi*, **Denetim** ' e basın ve proje adına tıklayın. **Araçlar**  >  **dosya düzenleme**sayfasına gidin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Derleme** derleyicisi > **Proje seçenekleri** iletişim kutusunu açın > **Compiler**
* **Genel Seçenekler** bölümünün altındaki **XML oluştur belge** kutusunu işaretleyin

::: moniker-end

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Veri açıklamaları

::: moniker range="<= aspnetcore-2.0"

NSwag [yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullandığından ve Web API 'si eylemleri için önerilen dönüş türü [ıactionresult](xref:Microsoft.AspNetCore.Mvc.IActionResult)olduğundan, eyleminiz ne yaptığını ve döndürdüğü şeyi çıkarmaz.

Aşağıdaki örneği inceleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Önceki eylem döndürülür `IActionResult` , ancak eylemin Içinde [Createdatroute](xref:System.Web.Http.ApiController.CreatedAtRoute*) veya [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)döndürüyor. İstemcilere bu eylemin hangi HTTP durum kodlarına geri dönebileceği bilinmektedir olduğunu bildirmek için veri açıklamalarını kullanın. Eylemi aşağıdaki özniteliklerle işaretleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

NSwag [yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullandığından ve Web API 'si eylemleri için önerilen dönüş türü [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)ise, yalnızca tarafından tanımlanan dönüş türünü çıkarabilir `T` . Diğer olası dönüş türlerini otomatik olarak çıkarsanamıyor.

Aşağıdaki örneği inceleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Önceki eylem döndürülür `ActionResult<T>` . Eylemin içinde, [Createdatroute](xref:System.Web.Http.ApiController.CreatedAtRoute*)döndürüyor. Denetleyici özniteliğine sahip olduğundan [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) , bir [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) yanıtı da mümkündür. Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses). İstemcilere bu eylemin hangi HTTP durum kodlarına geri dönebileceği bilinmektedir olduğunu bildirmek için veri açıklamalarını kullanın. Eylemi aşağıdaki özniteliklerle işaretleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

ASP.NET Core 2,2 veya üzeri sürümlerde, tek tek eylemleri ile açıkça dekorasyon yerine kuralları kullanabilirsiniz `[ProducesResponseType]` . Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.

::: moniker-end

Swagger Oluşturucusu artık bu eylemi doğru bir şekilde tanımlayabilir ve üretilen istemciler, uç noktayı çağırırken ne elde ettikleri hakkında bilgi alabilir. Öneri olarak tüm eylemleri bu özniteliklerle işaretleyin.

API eylemlerinizin dönmesi gereken HTTP yanıtlarının hakkında yönergeler için bkz. [RFC 7231 belirtimi](https://tools.ietf.org/html/rfc7231#section-4.3).
