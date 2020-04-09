---
title: Swashbuckle ve ASP.NET Core ile başlayın
author: zuckerthoben
description: Swagger UI'yi entegre etmek için ASP.NET Core web API projenize Swashbuckle'ı nasıl ekleyeceğinizi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661303"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle ve ASP.NET Core ile başlayın

Yazar: [Shayne Boyer](https://twitter.com/spboyer) ve [Scott Addie](https://twitter.com/Scott_Addie)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Swashbuckle'ın üç temel bileşeni vardır:

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): JSON uç noktaları olarak `SwaggerDocument` nesneleri ortaya çıkarmak için bir Swagger nesne modeli ve middleware.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): `SwaggerDocument` doğrudan rotaları, denetleyicileri ve modelleri nesneleri inşa eden bir Swagger jeneratör. Bu genellikle Swagger JSON uç noktasını otomatik olarak kullanıma sunmak için Swagger uç noktası ara katmanıyla birlikte kullanılır.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI aracının gömülü bir versiyonu. Swagger JSON uç noktasını yorumlayarak web API'sinin işlevlerini tanımlayan zengin ve özelleştirilebilir bir deneyim oluşturur. Genel yöntemler için yerleşik test kuluçkaları içerir.

## <a name="package-installation"></a>Paket kurulumu

Swashbuckle aşağıdaki yaklaşımlarla eklenebilir:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Paket **Yöneticisi Konsolu** penceresinden:
  * **Diğer Windows** > **Paket Yöneticisi Konsolu'na** **Git** > 
  * *TodoApi.csproj* dosyasının bulunduğu dizine gidin
  * Aşağıdaki komutu yürütün:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* **NuGet Paketlerini Yönet** iletişim kutusundan:
  * **Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın
  * Paket **kaynağını** "nuget.org" olarak ayarlama
  * "Ön sürüm ekle" seçeneğinin etkin olduğundan emin olun
  * Arama kutusuna "Swashbuckle.AspNetCore" girin
  * **Gözat** sekmesinden en son "Swashbuckle.AspNetCore" paketini seçin ve **Yükle'yi** tıklatın

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm Pad** > Ekle Paketleri'ndeki *Paketler* klasörüne sağ**tıklayın...**
* Paketleri **Ekle** penceresinin **Kaynak** açılır penceresini "nuget.org" olarak ayarlama
* "Ön sürüm paketlerini göster" seçeneğinin etkin olduğundan emin olun
* Arama kutusuna "Swashbuckle.AspNetCore" girin
* Sonuç bölmesinden en son "Swashbuckle.AspNetCore" paketini seçin ve **Paket Ekle'yi** tıklatın

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**Entegre Terminal'den**aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Şu komutu çalıştırın:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Swagger ara yazılım ekleme ve yapılandırma

`Startup` Sınıfta, sınıfı kullanmak için aşağıdaki ad `OpenApiInfo` alanını içeri aktarın:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Yöntemde hizmet koleksiyonuna Swagger `Startup.ConfigureServices` jeneratörü ekleyin:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

`Startup.Configure` Yöntemde, oluşturulan JSON belgesive Swagger UI hizmet için ara yazılım etkinleştirin:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

Önceki `UseSwaggerUI` yöntem arama statik [dosya middleware](xref:fundamentals/static-files)sağlar. .NET Framework veya .NET Core 1.x hedeflemesi durumunda, [projeye Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet paketini ekleyin.

Uygulamayı başlatın ve `http://localhost:<port>/swagger/v1/swagger.json`'ye gidin. Uç noktaları açıklayan oluşturulan belge [Swagger belirtiminde (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)gösterildiği gibi görünür.

Swagger UI bulunabilir. `http://localhost:<port>/swagger` Swagger UI ile API'yi keşfedin ve diğer programlara dahil edin.

> [!TIP]
> Swagger UI'ye uygulamanın kökünden`http://localhost:<port>/`hizmet etmek `RoutePrefix` için ( ), özelliği boş bir dize olarak ayarlayın:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

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

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:

![Sürüm bilgileriyle Swagger Kullanıcı Özel Birimi: açıklama, yazar ve daha fazla bağlantı görün](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML açıklamaları

XML yorumları aşağıdaki yaklaşımlarla etkinleştirilebilir:

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* **Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* **Solution Explorer'da** projeyi sağ tıklatın ve **Özellikler'i**seçin.
* **Yap** sekmesinin **Çıktı** bölümü altındaki **XML dokümantasyon dosya** kutusunu işaretleyin.

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın. **Araçlar** > **Dosyayı Edit'e**gidin.
* Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* > **Yapı** **Derleyicisi** > **Proje Seçenekleri** iletişim kutusunu açın
* **Genel Seçenekler** bölümünün altındaki **XML dokümantasyon** kutusunu oluştur'u işaretleyin

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

XML yorumlarını etkinleştirmek, belgesiz genel türler ve üyeler için hata ayıklama bilgileri sağlar. Belgelenmemiş türleri ve üyeleri uyarı iletisi ile gösterilir. Örneğin, aşağıdaki ileti uyarı kodu 1591 ihlalini gösterir:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Proje genelinde ki uyarıları bastırmak için, proje dosyasında yoksayılması gereken yarı sütunlu sınırlı bir uyarı kodları listesi tanımlayın. [C# varsayılan](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) değerlerini `$(NoWarn);` de uygular için uyarı kodlarını ekler.

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

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

Önceki kodda, [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection) web API projesiyle eşleşen bir XML dosya adı oluşturmak için kullanılır. [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) özelliği XML dosyasına bir yol oluşturmak için kullanılır. Bazı Swagger özellikleri (örneğin, giriş parametrelerinin şema veya ilgili özniteliklerden HTTP yöntemleri ve yanıt kodları) bir XML dokümantasyon dosyası kullanmadan çalışır. Çoğu özellik, yani yöntem özetleri ve parametrelerin ve yanıt kodlarının açıklamaları için, bir XML dosyasının kullanılması zorunludur.

Bir eyleme üç eğik çizgiyle açıklama eklediğinizde bölüm üst bilgisine açıklama eklenir ve Swagger UI geliştirilir. Eylemin üzerine [ \<bir özet>](/dotnet/csharp/programming-guide/xmldoc/summary) öğesi ekleyin: `Delete`

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger Kullanıcı İyiucu Su Birimi, önceki kodun `<summary>` öğesinin iç metnini görüntüler:

![XML yorumunu gösteren Swagger UI 'Belirli bir TodoItem'i siler.' DELETE yöntemi için](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Bu ek açıklamalar ile UI geliştirmeleri dikkat edin:

![Gösterilen ek yorumlar ile Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Veri açıklamaları

Swagger UI bileşenlerini sürücüye yardımcı olmak için [modeli System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) ad alanında bulunan özniteliklerle işaretleyin.

`[Required]` `Name` Sınıfın özelliğine öznitelik `TodoItem` ekleyin:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

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

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

**Yanıt İçerik Türü** açılır bu içerik türünü denetleyicinin GET eylemleri için varsayılan olarak seçer:

![Varsayılan yanıt içeriği türüne sahip Swagger Kullanıcı BiraSı](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Web API'deki veri ek açıklamalarının kullanımı arttıkça, UI ve API yardım sayfaları daha açıklayıcı ve yararlı hale gelir.

### <a name="describe-response-types"></a>Yanıt türlerini açıklayın

Web API'si tüketen geliştiriciler en&mdash;çok yanıt türleri ve hata kodları (standart değilse) döndürülenlerle ilgilenirler. Yanıt türleri ve hata kodları XML yorumlarında ve veri ek açıklamalarında gösterilir.

Eylem `Create` başarı bir HTTP 201 durum kodu döndürür. Deftere nakledilen istek gövdesi null olduğunda bir HTTP 400 durum kodu döndürülür. Swagger UI uygun belgeler olmadan, tüketici bu beklenen sonuçlar hakkında bilgi yoksundur. Aşağıdaki örnekte vurgulanan satırları ekleyerek bu sorunu giderin:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Swagger UI şimdi açıkça beklenen HTTP yanıt kodları belgeler:

![POSTA Yanıt Sınıfı açıklamasını gösteren Swagger UI 'Yeni oluşturulan Todo öğesini döndürür' ve '400 - Öğe null ise' durum kodu ve Yanıt İletileri altında neden için](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

Core 2.2 veya daha sonra ASP.NET, sözleşmeler açıkça ile bireysel eylemleri `[ProducesResponseType]`dekorasyon alternatif olarak kullanılabilir . Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Kullanıcı arabirimini özelleştirme

Stok UI hem işlevsel hem de sunulabilir. Ancak, API dokümantasyon sayfaları markanızı veya temanızı temsil etmelidir. Swashbuckle bileşenlerinin markalandırılması, statik dosyalara hizmet verecek kaynakların eklenmesini ve bu dosyaları barındıracak klasör yapısını oluşturmayı gerektirir.

.NET Framework veya .NET Core 1.x hedeflemesi durumunda, [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet paketini projeye ekleyin:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

.NET Core 2.x'i hedefleyen ve [meta paketi](xref:fundamentals/metapackage)kullanılarak önceki NuGet paketi zaten yüklenmiş.

Statik Dosya Middleware etkinleştirin:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

[Swagger UI GitHub deposundan](https://github.com/swagger-api/swagger-ui/tree/master/dist) *dist* klasörünün içeriğini edinin. Bu klasör, Swagger UI sayfası için gerekli varlıkları içerir.

Bir *wwwroot/swagger/ui* klasörü oluşturun ve *dist* klasörünün içeriğini kopyalayın.

Sayfa üstbilgisini özelleştirmek için aşağıdaki CSS ile *wwwroot/swagger/ui'de* *bir custom.css* dosyası oluşturun:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Diğer CSS dosyalarından sonra, ui klasörü içindeki *index.html* dosyasında *custom.css'ye* başvurun:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

*index.html* sayfasına göz `http://localhost:<port>/swagger/ui/index.html`atın. Üstbilginin metin kutusuna girin `https://localhost:<port>/swagger/v1/swagger.json` ve **Keşfet** düğmesini tıklatın. Ortaya çıkan sayfa aşağıdaki gibi görünür:

![Özel üstbilgi başlığı ile Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

Sayfayla yapabileceğiniz çok şey var. [Swagger UI GitHub deposundaki](https://github.com/swagger-api/swagger-ui)UI kaynaklarıiçin tam yetenekleri görün.
