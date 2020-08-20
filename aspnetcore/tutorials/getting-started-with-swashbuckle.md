---
title: Swashbuckle ve ASP.NET Core kullanmaya başlayın
author: zuckerthoben
description: Swagger Kullanıcı arabirimini bütünleştirmek için ASP.NET Core Web API Projenize swashbuckle ekleme hakkında bilgi edinin.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 84094dce306e10470ec7b7be3efe6da024470330
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628852"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle ve ASP.NET Core kullanmaya başlayın

, [Shayne Boyer](https://twitter.com/spboyer) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Swashbuckle'ın üç temel bileşeni vardır:

* [Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` nesneleri JSON uç noktaları olarak göstermek için Swagger nesne modeli ve ara yazılımı.

* [Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): `SwaggerDocument` nesneleri doğrudan rotalarınız, Denetleyicilerinizden ve modellerden oluşturan bir Swagger Oluşturucu. Bu genellikle Swagger JSON uç noktasını otomatik olarak kullanıma sunmak için Swagger uç noktası ara katmanıyla birlikte kullanılır.

* [Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger Kullanıcı arabirimi aracının gömülü bir sürümü. Swagger JSON uç noktasını yorumlayarak web API'sinin işlevlerini tanımlayan zengin ve özelleştirilebilir bir deneyim oluşturur. Genel yöntemler için yerleşik test kuluçkaları içerir.

## <a name="package-installation"></a>Paket yüklemesi

Aşağıdaki yaklaşımlar ile swashbuckle eklenebilir:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Paket Yöneticisi konsol** penceresinde:
  * **View**  >  **Diğer Windows**  >  **Paket Yöneticisi konsolunu** görüntüle ' ye git
  * *TodoApi. csproj* dosyasının bulunduğu dizine gidin
  * Şu kodu yürütün:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* **NuGet Paketlerini Yönet** iletişim kutusunda:
  * **Çözüm Gezgini**  >  **NuGet Paketlerini Yönet** ' de projeye sağ tıklayın
  * **Paket kaynağını** "NuGet.org" olarak ayarlayın
  * "Ön sürümü dahil et" seçeneğinin etkinleştirildiğinden emin olun
  * Arama kutusuna "swashbuckle. AspNetCore" yazın
  * **Araştır** sekmesinden en son "swashbuckle. aspnetcore" paketini seçin ve sonra da **yüklensin** ' e tıklayın.

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* *Packages* **Çözüm bölmesi**  >  **paket Ekle...** ' da paketler klasörüne sağ tıklayın.
* **Paket Ekle** penceresinin **kaynak** açılan penceresini "NuGet.org" olarak ayarlayın
* "Yayın öncesi paketleri göster" seçeneğinin etkin olduğundan emin olun
* Arama kutusuna "swashbuckle. AspNetCore" yazın
* Sonuçlar bölmesinden en son "swashbuckle. AspNetCore" paketini seçin ve **paket Ekle** ' ye tıklayın.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Tümleşik terminalden**aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

Yöntemdeki Services koleksiyonuna Swagger oluşturucuyu ekleyin `Startup.ConfigureServices` :

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

`Startup.Configure`Yönteminde, oluşturulan JSON belgesine ve Swagger Kullanıcı arabirimine hizmet veren ara yazılımı etkinleştirin:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> Swashbuckle <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> , yolları ve uç noktalarını öğrenmek IÇIN MVC 'yi kullanır. Proje çağrıları <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , rotalar ve uç noktaları otomatik olarak keşfedilir. Çağrıldığında <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> , <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> yönteminin açıkça çağrılması gerekir. Daha fazla bilgi için bkz. [swashbuckle, ApiExplorer ve yönlendirme](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).

Önceki `UseSwaggerUI` Yöntem çağrısı [statik dosya ara yazılımını](xref:fundamentals/static-files)sunar. .NET Framework veya .NET Core 1. x 'i hedefliyorsanız, projeye [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.

Uygulamayı başlatın ve adresine gidin `http://localhost:<port>/swagger/v1/swagger.json` . Uç noktaları tanımlayan oluşturulan belge, [Openapı belirtiminde (openapi.jsüzerinde)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson)gösterildiği gibi görüntülenir.

Swagger Kullanıcı arabirimi adresinde bulunabilir `http://localhost:<port>/swagger` . Swagger Kullanıcı arabirimi aracılığıyla API 'YI keşfet ve diğer programlarda birleştirme.

> [!TIP]
> Swagger Kullanıcı arabirimine uygulamanın kökünde () hizmeti sağlamak için `http://localhost:<port>/` , `RoutePrefix` özelliğini boş bir dizeye ayarlayın:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

IIS veya ters proxy ile dizin kullanıyorsanız, Swagger uç noktasını, öneki kullanılarak göreli bir yol olarak ayarlayın `./` . Örneğin, `./swagger/v1/swagger.json`. Kullanarak `/swagger/v1/swagger.json` , UYGULAMANıN URL 'nin gerçek KÖKÜNDE json dosyasını aramasını söyler (Ayrıca kullanılıyorsa rota öneki). Örneğin `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` yerine `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` kullanın.

> [!NOTE]
> Varsayılan olarak, swashbuckle, tam olarak &mdash; Openapı belirtimi olarak adlandırılan belirtiminin 3,0 sürümünde Swagger JSON oluşturur ve kullanıma sunar. Geriye dönük uyumluluğu desteklemek için, bunun yerine 2,0 biçiminde JSON 'u açığa çıkarmayı tercih edebilirsiniz. Bu 2,0 biçimi, Microsoft Power Apps ve şu anda Openapı sürüm 2,0 ' i destekleyen Microsoft Flow gibi Tümleştirmeler için önemlidir. 2,0 biçimini kabul etmek için özelliği ' de ayarlayın `SerializeAsV2` `Startup.Configure` :
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a>Özelleştirme ve genişletme

Swagger, nesne modelini belgeleme ve Kullanıcı arabirimini temanızla eşleşecek şekilde özelleştirme seçenekleri sağlar.

`Startup`Sınıfında, aşağıdaki ad alanlarını ekleyin:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>API bilgisi ve açıklaması

Yöntemine geçirilen yapılandırma eylemi `AddSwaggerGen` Yazar, lisans ve açıklama gibi bilgileri ekler:

`Startup`Sınıfında, sınıfını kullanmak için aşağıdaki ad alanını içeri aktarın `OpenApiInfo` :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Sınıfını kullanarak `OpenApiInfo` , Kullanıcı arabiriminde görünen bilgileri değiştirin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger Kullanıcı arabirimi, sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı arabirimi: Açıklama, yazar ve daha fazla bağlantı görüntüle](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML açıklamaları

XML açıklamaları aşağıdaki yaklaşımlar ile etkinleştirilebilir:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **Çözüm Gezgini** projeye sağ tıklayın ve **>. csproj Project_Name <Düzenle**' yi seçin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Çözüm Gezgini** ' de projeye sağ tıklayın ve **Özellikler**' i seçin.
* **Build** sekmesinin **output** bölümünün altındaki **XML belge dosyası** kutusunu işaretleyin.

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* *Çözüm bölmesi*, **Denetim** ' e basın ve proje adına tıklayın. **Araçlar**  >  **dosya düzenleme**sayfasına gidin.
* Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Derleme** derleyicisi > **Proje seçenekleri** iletişim kutusunu açın > **Compiler**
* **Genel Seçenekler** bölümünün altındaki **XML oluştur belge** kutusunu işaretleyin

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vurgulanan satırları *. csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

XML açıklamalarını etkinleştirmek, belgelenmemiş ortak türler ve Üyeler için hata ayıklama bilgileri sağlar. Belgelenmemiş türler ve Üyeler uyarı iletisiyle belirtilir. Örneğin, aşağıdaki ileti 1591 uyarı kodunu ihlal eder:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Uyarıları proje genelinde gizlemek için, proje dosyasında yoksayılacak uyarı kodlarının noktalı virgülle ayrılmış bir listesini tanımlayın. Yalnızca `$(NoWarn);` [C# varsayılan değerlerini](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) uygulamak için uyarı kodlarını eklemek.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Yalnızca belirli Üyeler için uyarıları gizlemek için, kodu [#pragma uyarı](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) Önişlemci yönergeleri arasına alın. Bu yaklaşım, API belgeleri aracılığıyla sunulmaması gereken kod için yararlıdır. Aşağıdaki örnekte, tüm sınıf için uyarı kodu CS1591 yok sayılır `Program` . Uyarı kodu zorlaması sınıf tanımının kapandığına geri yüklenir. Virgülle ayrılmış bir liste ile birden çok uyarı kodu belirtin.

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

Swagger 'yi yukarıdaki yönergelerle oluşturulan XML dosyasını kullanacak şekilde yapılandırın. Linux veya Windows dışı işletim sistemleri için dosya adları ve yolları büyük/küçük harfe duyarlı olabilir. Örneğin, bir *TodoApi.XML* dosyası Windows üzerinde geçerlidir ancak CentOS değildir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

Yukarıdaki kodda, [yansıma](/dotnet/csharp/programming-guide/concepts/reflection) , Web API projesi ile eşleşen bir XML dosya adı oluşturmak için kullanılır. [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) ÖZELLIĞI, XML dosyasının yolunu oluşturmak için kullanılır. Bazı Swagger özellikleri (örneğin, bir XML belge dosyası kullanılmadan, giriş parametrelerinin veya HTTP yöntemlerinin ve yanıt kodlarının) bir bölümü çalışır. Çoğu özellik için, yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları, bir XML dosyası kullanımı zorunludur.

Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir. [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary)Eylemin üstüne bir öğe ekleyin `Delete` :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger Kullanıcı arabirimi, önceki kodun öğesinin iç metnini görüntüler `<summary>` :

![XML açıklamasını gösteren Swagger Kullanıcı arabirimi, belirli bir TodoItem siler. DELETE yöntemi için](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Kullanıcı arabirimi, oluşturulan JSON şeması tarafından çalıştırılır:

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

[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) `Create` Eylem yöntemi belgelerine bir öğesi ekleyin. Öğesinde belirtilen bilgileri tamamlar `<summary>` ve daha sağlam bir Swagger Kullanıcı arabirimi sağlar. `<remarks>`Öğe içeriği metin, JSON veya XML içerebilir.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Bu ek açıklamalarla UI geliştirmelerini göz unutmayın:

![Ek açıklamaların gösterildiği Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Veri açıklamaları

Swagger Kullanıcı Arabirimi bileşenlerini sağlamaya yardımcı olmak için [System. ComponentModel. Dataaçıklamalarda](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan öznitelikleri olan modeli işaretleyin.

`[Required]`Özniteliğini `Name` sınıfının özelliğine ekleyin `TodoItem` :

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Bu özniteliğin varlığı, Kullanıcı arabirimi davranışını değiştirir ve temel alınan JSON şemasını değiştirir:

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

`[Produces("application/json")]`ÖZNITELIĞI API denetleyicisine ekleyin. Amaç, denetleyicinin eylemlerinin bir *uygulama/JSON*yanıt içerik türünü desteklediğini bildirsağlamaktır:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

**Yanıt Içerik türü** açılan liste, denetleyicinin al eylemleri için varsayılan olarak bu içerik türünü seçer:

![Varsayılan yanıt içerik türüyle Swagger Kullanıcı arabirimi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Web API 'sindeki veri ek açıklamaların kullanımı arttıkça, UI ve API Yardım sayfaları daha açıklayıcı ve yararlı hale gelir.

### <a name="describe-response-types"></a>Yanıt türlerini açıkla

Bir Web API 'SI kullanan geliştiriciler, &mdash; özellikle yanıt türleri ve hata kodları (Standart değilse) döndürülmesiyle ilgilidir. Yanıt türleri ve hata kodları XML açıklamaları ve veri ek açıklamalarında gösterilir.

`Create`Eylem başarılı olduğunda BIR HTTP 201 durum kodu döndürür. Postalanan istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür. Swagger Kullanıcı arabiriminde doğru belgeler olmadan, tüketici beklenen bu sonuçlar hakkında bilgi sahibi yoktur. Aşağıdaki örneğe vurgulanan satırları ekleyerek bu sorunu giderebilirsiniz:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Swagger Kullanıcı arabirimi artık beklenen HTTP yanıt kodlarını açıkça belgelemektedir:

![Yanıt Iletileri ' ndeki durum kodu ve nedeni için, POST Response sınıfının Description ', yeni oluşturulan Todo öğesini döndürür ve ' 400-öğesi null ise '](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

ASP.NET Core 2,2 veya üzeri sürümlerde kurallar, ile tek tek eylemleri açıkça dekorasyon alternatifi olarak kullanılabilir `[ProducesResponseType]` . Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.

Deseni desteklemek için `[ProducesResponseType]` , [swashbuckle. Aspnetcore. açıklamalarda](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) paketi, yanıt, şema ve parametre meta verilerini etkinleştirmek ve zenginleştirmek için uzantılar sağlar.

::: moniker-end

### <a name="customize-the-ui"></a>Kullanıcı arabirimini özelleştirme

Varsayılan Kullanıcı arabirimi hem işlevsel hem de edileni. Ancak, API belge sayfaları markanızı veya temanızı temsil etmelidir. Marka, swashbuckle bileşenleri, statik dosyalara ve bu dosyaları barındırmak için klasör yapısını oluşturmaya yönelik kaynakların eklenmesini gerektirir.

.NET Framework veya .NET Core 1. x 'i hedefliyorsanız, [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet paketini projeye ekleyin:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Önceki NuGet paketi, .NET Core 2. x hedefleniyorsa ve [metapackage](xref:fundamentals/metapackage)kullanılarak zaten yüklüdür.

Statik dosya ara yazılımını etkinleştir:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Ek CSS stil sayfaları eklemek için, bunları projenin *Wwwroot* klasörüne ekleyin ve ara yazılım seçeneklerinde göreli yolu belirtin:

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
