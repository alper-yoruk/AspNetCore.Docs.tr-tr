---
title: ASP.NET Core ile web API’leri oluşturma
author: scottaddie
description: ASP.NET Core ' de Web API 'SI oluşturmanın temellerini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 7c9762d23ff612155846357bfadeaaad492c7299
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404736"
---
# <a name="create-web-apis-with-aspnet-core"></a>ASP.NET Core ile web API’leri oluşturma

[Scott Ade](https://github.com/scottaddie) ve [Tom Dykstra](https://github.com/tdykstra) tarafından

ASP.NET Core, web API'leri olarak da bilinen RESTful hizmetleri oluşturulmasını C# kullanarak desteklemektedir. İstekleri işlemek için, bir Web API 'SI denetleyicileri kullanır. Bir Web API 'sindeki *denetleyiciler* öğesinden türetilen sınıflardır `ControllerBase` . Bu makalede, Web API isteklerini işlemek için denetleyicilerin nasıl kullanılacağı gösterilmektedir.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([İndirme](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>ControllerBase sınıfı

Bir Web API 'SI, öğesinden türetilen bir veya daha fazla denetleyici sınıfından oluşur <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Web API proje şablonu bir başlatıcı denetleyicisi sağlar:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Sınıfından türeterek bir Web API denetleyicisi oluşturmayın <xref:Microsoft.AspNetCore.Mvc.Controller> . `Controller`' dan türetilir `ControllerBase` ve görünümler için destek ekler, bu nedenle Web API istekleri için değil Web sayfalarını işlemeye yöneliktir. Bu kural için bir özel durum var: aynı denetleyiciyi hem görünümler hem de Web API 'Leri için kullanmayı planlıyorsanız, öğesinden türetirsiniz `Controller` .

`ControllerBase`Sınıfı, http isteklerini işlemek için yararlı olan birçok özellik ve yöntem sağlar. Örneğin, `ControllerBase.CreatedAtAction` bir 201 durum kodu döndürür:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Sağladığı yöntemlere ilişkin bazı örnekler aşağıda verilmiştir `ControllerBase` .

|Yöntem   |Notlar    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| 400 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|404 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Bir dosya döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|[Model bağlamasını](xref:mvc/models/model-binding)çağırır.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|[Model doğrulamasını](xref:mvc/models/validation)çağırır.|

Tüm kullanılabilir yöntemlerin ve özelliklerin listesi için bkz <xref:Microsoft.AspNetCore.Mvc.ControllerBase> ..

## <a name="attributes"></a>Öznitelikler

<xref:Microsoft.AspNetCore.Mvc>Ad alanı, Web API denetleyicileri ve eylem yöntemlerinin davranışını yapılandırmak için kullanılabilecek öznitelikleri sağlar. Aşağıdaki örnek, desteklenen HTTP eylem fiilini ve döndürülebilecek bilinen HTTP durum kodlarını belirtmek için özniteliklerini kullanır:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Aşağıda, kullanılabilecek özniteliklerin daha fazla örneği verilmiştir.

|Öznitelik|Notlar|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Bir denetleyicinin veya eylemin URL modelini belirtir.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Model bağlama için dahil edilecek öneki ve özellikleri belirtir.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |HTTP GET ACTION fiilini destekleyen bir eylemi tanımlar.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Bir eylemin kabul ettiği veri türlerini belirtir.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Bir eylemin döndürdüğü veri türlerini belirtir.|

Kullanılabilir öznitelikleri içeren bir liste için, bkz <xref:Microsoft.AspNetCore.Mvc> . ad alanı.

## <a name="apicontroller-attribute"></a>ApiController özniteliği

[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Özniteliği bir denetleyici sınıfına uygulanabilir ve bu, API 'ye özgü aşağıdaki davranışları etkinleştirmek için kullanılabilir:

::: moniker range=">= aspnetcore-2.2"

* [Öznitelik yönlendirme gereksinimi](#attribute-routing-requirement)
* [Otomatik HTTP 400 yanıtları](#automatic-http-400-responses)
* [Bağlama kaynak parametresi çıkarımı](#binding-source-parameter-inference)
* [Multipart/form-veri isteği çıkarımı](#multipartform-data-request-inference)
* [Hata durum kodları için sorun ayrıntıları](#problem-details-for-error-status-codes)

*Hata durum kodları özelliği Için sorun ayrıntıları* , 2,2 veya üzeri bir [Uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir. Diğer özellikler, 2,1 veya üzeri bir uyumluluk sürümü gerektirir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Öznitelik yönlendirme gereksinimi](#attribute-routing-requirement)
* [Otomatik HTTP 400 yanıtları](#automatic-http-400-responses)
* [Bağlama kaynak parametresi çıkarımı](#binding-source-parameter-inference)
* [Multipart/form-veri isteği çıkarımı](#multipartform-data-request-inference)

Bu özellikler, 2,1 veya üzeri bir [Uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Belirli denetleyicilerde öznitelik

`[ApiController]`Özniteliği, proje şablonundan aşağıdaki örnekte olduğu gibi belirli denetleyicilere uygulanabilir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Birden çok denetleyicilerde öznitelik

Özniteliği birden fazla denetleyicide kullanmanın bir yaklaşımı, özniteliğiyle birlikte açıklanan özel bir temel denetleyici sınıfı oluşturmaktır `[ApiController]` . Aşağıdaki örnekte, özel bir temel sınıf ve ondan türetilen bir denetleyici gösterilmektedir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Bir derlemedeki öznitelik

[Uyumluluk sürümü](xref:mvc/compatibility-version) 2,2 veya üzeri bir sürüme ayarlandıysa, `[ApiController]` öznitelik bir derlemeye uygulanabilir. Bu şekilde ek açıklama, derlemedeki tüm denetleyicilere Web API davranışını uygular. Tek tek denetleyiciler için geri alma yöntemi yoktur. Derleme düzeyi özniteliğini sınıfı çevreleyen ad alanı bildirimine uygulayın `Startup` :

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Öznitelik yönlendirme gereksinimi

`[ApiController]`Öznitelik, öznitelik yönlendirme bir gereksinim yapar. Örneğin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Eylemlere,, veya içinde tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla erişilemez `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> `Startup.Configure` .

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Eylemlere, veya içinde tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> erişilemez <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> `Startup.Configure` .

::: moniker-end

## <a name="automatic-http-400-responses"></a>Otomatik HTTP 400 yanıtları

`[ApiController]`Öznitelik, model doğrulama hatalarının otomatik olarak BIR HTTP 400 yanıtı tetiklenmesine neden olur. Sonuç olarak, aşağıdaki kod bir eylem yönteminde gereksizdir:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC, <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> önceki denetimi yapmak için eylem filtresini kullanır.

### <a name="default-badrequest-response"></a>Varsayılan BadRequest yanıtı

Uyumluluk sürümü 2,1 ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.SerializableError> . Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

2,2 veya üzeri bir uyumluluk sürümü ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

`ValidationProblemDetails`Tür:

* Web API yanıtlarında hata belirtmek için makine tarafından okunabilen bir biçim sağlar.
* [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807)uyar.

::: moniker-end

### <a name="log-automatic-400-responses"></a>Otomatik 400 yanıtlarını günlüğe kaydet

Bkz. [otomatik 400 yanıtlarını model doğrulama hatalarında günlüğe kaydetme (ASPNET/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Otomatik 400 yanıtını devre dışı bırak

Otomatik 400 davranışını devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> özelliğini olarak ayarlayın `true` . Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Bağlama kaynak parametresi çıkarımı

Bağlama kaynak özniteliği, bir eylem parametresi değerinin bulunduğu konumu tanımlar. Aşağıdaki bağlama kaynak öznitelikleri var:

|Öznitelik|Bağlama kaynağı |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | İstek gövdesi |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | İstek gövdesinde form verileri |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | İstek üst bilgisi |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | İstek sorgusu dize parametresi |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Geçerli istekten veri yönlendir |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Eylem parametresi olarak eklenen istek hizmeti |

> [!WARNING]
> `[FromRoute]`Değerler `%2f` (yani) içerdiğinde kullanmayın `/` . `%2f`atlanmaz `/` . `[FromQuery]`Değer içermesi gerekiyorsa kullanın `%2f` .

`[ApiController]`Özniteliği veya gibi bağlama kaynak öznitelikleri olmadan `[FromQuery]` , ASP.NET Core çalışma zamanı karmaşık nesne modeli cildi kullanmaya çalışır. Karmaşık nesne modeli Ciltçi, verileri değer sağlayıcılarından tanımlı bir düzende çeker.

Aşağıdaki örnekte `[FromQuery]` öznitelik, `discontinuedOnly` istek URL 'sinin sorgu dizesinde parametre değerinin sağlandığını gösterir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]`Öznitelik, eylem parametrelerinin varsayılan veri kaynakları için çıkarım kurallarını uygular. Bu kurallar, eylem parametrelerine öznitelikleri uygulayarak bağlama kaynaklarını el ile tanımlamak zorunda kalmadan sizi kaydeder. Bağlama kaynak çıkarımı kuralları aşağıdaki gibi davranır:

* `[FromBody]`karmaşık tür parametreleri için algılanır. Çıkarım kuralı için bir özel durum, `[FromBody]` ve gibi özel bir anlamı olan karmaşık, yerleşik bir türdür <xref:Microsoft.AspNetCore.Http.IFormCollection> <xref:System.Threading.CancellationToken> . Bağlama kaynak çıkarımı kodu bu özel türleri yoksayar.
* `[FromForm]`, ve türündeki eylem parametreleri için algılanır <xref:Microsoft.AspNetCore.Http.IFormFile> <xref:Microsoft.AspNetCore.Http.IFormFileCollection> . Bu, herhangi bir basit veya Kullanıcı tanımlı tür için çıkarsanamıyor.
* `[FromRoute]`yol şablonundaki bir parametreyle eşleşen herhangi bir eylem parametresi adı için algılanır. Birden fazla yol bir eylem parametresiyle eşleştiğinde, herhangi bir rota değeri kabul edilir `[FromRoute]` .
* `[FromQuery]`diğer eylem parametreleri için algılanır.

### <a name="frombody-inference-notes"></a>FromBody çıkarım notları

`[FromBody]`, veya gibi basit türler için çıkarsanamıyor `string` `int` . Bu nedenle, bu `[FromBody]` işlev gerektiğinde basit türler için özniteliği kullanılmalıdır.

Bir eylem, istek gövdesinden birden fazla parametre bağlamışsa, bir özel durum oluşturulur. Örneğin, aşağıdaki eylem yöntemi imzalarının tümü bir özel duruma neden olur:

* `[FromBody]`karmaşık türler olduklarından her ikisi de üzerinde algılanır.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]`karmaşık bir tür olduğundan, diğeri üzerinde olan özniteliği.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]`her ikisinde de özniteliği.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> ASP.NET Core 2,1 ' de, listeler ve diziler gibi koleksiyon türü parametreleri yanlış olarak algılanır `[FromQuery]` . `[FromBody]`Öznitelik, istek gövdesinden bağlanmaları durumunda bu parametreler için kullanılmalıdır. Bu davranış ASP.NET Core 2,2 veya sonraki bir sürümde düzeltilir. burada, koleksiyon türü parametrelerinin varsayılan olarak gövdeden bağlanacak şekilde çıkarsandır.

::: moniker-end

### <a name="disable-inference-rules"></a>Çıkarım kurallarını devre dışı bırak

Bağlama kaynak çıkarımını devre dışı bırakmak için, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> olarak ayarlayın `true` . Aşağıdaki kodu içine ekleyin `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Multipart/form-veri isteği çıkarımı

Özniteliği `[ApiController]` ile bir eylem parametresine ek açıklama eklendiğinde öznitelik bir çıkarım kuralı uygular [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) . `multipart/form-data`İstek içerik türü çıkarsandı.

Varsayılan davranışı devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> özelliğini `true` olarak ayarlayın `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Hata durum kodları için sorun ayrıntıları

Uyumluluk sürümü 2,2 veya üzeri olduğunda, MVC bir hata sonucunu (durum kodu 400 veya üzeri olan bir sonuç) ile bir sonuçla dönüştürür <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> . `ProblemDetails`Türü, BIR http yanıtında makine tarafından okunabilen hata ayrıntılarını sağlamak Için [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807) dayalıdır.

Bir denetleyici eyleminde aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

`NotFound`Yöntemi, gövdesi olan BIR HTTP 404 durum kodu üretir `ProblemDetails` . Örneğin:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>ProblemDetails yanıtını devre dışı bırak

`ProblemDetails`Özelliği olarak ayarlandığında hata durumu kodlarının otomatik olarak oluşturulması devre dışı bırakılır <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> `true` . Aşağıdaki kodu içine ekleyin `Startup.ConfigureServices` :

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Desteklenen istek içerik türlerini [tüketir] özniteliğiyle tanımlayın

Varsayılan olarak, bir eylem tüm kullanılabilir istek içerik türlerini destekler. Örneğin, bir uygulama hem JSON hem de XML [giriş formatlarını](xref:mvc/models/model-binding#input-formatters)destekleyecek şekilde yapılandırıldıysa, bir eylem, ve dahil olmak üzere birden çok içerik türünü destekler `application/json` `application/xml` .

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar. Özniteliği bir `[Consumes]` eylem veya denetleyiciye uygulayarak bir veya daha fazla içerik türü belirtin:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

Yukarıdaki kodda, `CreateProduct` eylem içerik türünü belirtir `application/xml` . Bu eyleme yönlendirilen isteklerin bir `Content-Type` üst bilgisi belirtmesi gerekir `application/xml` . `Content-Type`Üst bilgisi belirtmeyen istekler, `application/xml` [415 desteklenmeyen medya türü](https://developer.mozilla.org/docs/Web/HTTP/Status/415) yanıtı ile sonuçlanır.

`[Consumes]`Öznitelik Ayrıca bir eylemin bir tür kısıtlaması uygulayarak bir gelen isteğin içerik türüne göre seçimini etkilemesini sağlar. Aşağıdaki örneği inceleyin:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

Önceki kodda, `ConsumesController` URL 'ye gönderilen istekleri işleyecek şekilde yapılandırılır `https://localhost:5001/api/Consumes` . Denetleyicinin eylemlerinin her ikisi de `PostJson` `PostForm` aynı URL 'ye sahip post isteklerini işler. `[Consumes]`Öznitelik bir tür kısıtlaması uygulamadan, belirsiz eşleşme özel durumu oluşur.

`[Consumes]`Özniteliği her iki eyleme de uygulanır. `PostJson`Eylem, üst bilgisi ile gönderilen istekleri işler `Content-Type` `application/json` . `PostForm`Eylem, üst bilgisi ile gönderilen istekleri işler `Content-Type` `application/x-www-form-urlencoded` . 

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
