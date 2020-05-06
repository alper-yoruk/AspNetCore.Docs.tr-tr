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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 5ea82d36d305b1f6ba58fba1b4c5bb4dca22f912
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776532"
---
# <a name="create-web-apis-with-aspnet-core"></a>ASP.NET Core ile web API’leri oluşturma

[Scott Ade](https://github.com/scottaddie) ve [Tom Dykstra](https://github.com/tdykstra) tarafından

ASP.NET Core, web API'leri olarak da bilinen RESTful hizmetleri oluşturulmasını C# kullanarak desteklemektedir. İstekleri işlemek için, bir Web API 'SI denetleyicileri kullanır. Bir Web API 'sindeki *denetleyiciler* öğesinden `ControllerBase`türetilen sınıflardır. Bu makalede, Web API isteklerini işlemek için denetleyicilerin nasıl kullanılacağı gösterilmektedir.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([İndirme](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>ControllerBase sınıfı

Bir Web API 'SI, öğesinden <xref:Microsoft.AspNetCore.Mvc.ControllerBase>türetilen bir veya daha fazla denetleyici sınıfından oluşur. Web API proje şablonu bir başlatıcı denetleyicisi sağlar:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.Controller> Sınıfından türeterek BIR Web API denetleyicisi oluşturmayın. `Controller`' dan `ControllerBase` türetilir ve görünümler için destek ekler, bu nedenle Web API istekleri için değil Web sayfalarını işlemeye yöneliktir. Bu kural için bir özel durum var: aynı denetleyiciyi hem görünümler hem de Web API 'Leri için kullanmayı planlıyorsanız, öğesinden `Controller`türetirsiniz.

Sınıfı `ControllerBase` , http isteklerini işlemek için yararlı olan birçok özellik ve yöntem sağlar. Örneğin, `ControllerBase.CreatedAtAction` bir 201 durum kodu döndürür:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

`ControllerBase` Sağladığı yöntemlere ilişkin bazı örnekler aşağıda verilmiştir.

|Yöntem   |Notlar    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| 400 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|404 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Bir dosya döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|[Model bağlamasını](xref:mvc/models/model-binding)çağırır.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|[Model doğrulamasını](xref:mvc/models/validation)çağırır.|

Tüm kullanılabilir yöntemlerin ve özelliklerin listesi için bkz <xref:Microsoft.AspNetCore.Mvc.ControllerBase>..

## <a name="attributes"></a>Öznitelikler

Ad <xref:Microsoft.AspNetCore.Mvc> alanı, Web API denetleyicileri ve eylem yöntemlerinin davranışını yapılandırmak için kullanılabilecek öznitelikleri sağlar. Aşağıdaki örnek, desteklenen HTTP eylem fiilini ve döndürülebilecek bilinen HTTP durum kodlarını belirtmek için özniteliklerini kullanır:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Aşağıda, kullanılabilecek özniteliklerin daha fazla örneği verilmiştir.

|Öznitelik|Notlar|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Bir denetleyicinin veya eylemin URL modelini belirtir.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Model bağlama için dahil edilecek öneki ve özellikleri belirtir.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |HTTP GET ACTION fiilini destekleyen bir eylemi tanımlar.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Bir eylemin kabul ettiği veri türlerini belirtir.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Bir eylemin döndürdüğü veri türlerini belirtir.|

Kullanılabilir öznitelikleri içeren bir liste için, bkz. <xref:Microsoft.AspNetCore.Mvc> ad alanı.

## <a name="apicontroller-attribute"></a>ApiController özniteliği

[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Özniteliği bir denetleyici sınıfına uygulanabilir ve bu, API 'ye özgü aşağıdaki davranışları etkinleştirmek için kullanılabilir:

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

`[ApiController]` Özniteliği, proje şablonundan aşağıdaki örnekte olduğu gibi belirli denetleyicilere uygulanabilir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Birden çok denetleyicilerde öznitelik

Özniteliği birden fazla denetleyicide kullanmanın bir yaklaşımı, `[ApiController]` özniteliğiyle birlikte açıklanan özel bir temel denetleyici sınıfı oluşturmaktır. Aşağıdaki örnekte, özel bir temel sınıf ve ondan türetilen bir denetleyici gösterilmektedir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Bir derlemedeki öznitelik

[Uyumluluk sürümü](xref:mvc/compatibility-version) 2,2 veya üzeri bir sürüme ayarlandıysa, `[ApiController]` öznitelik bir derlemeye uygulanabilir. Bu şekilde ek açıklama, derlemedeki tüm denetleyicilere Web API davranışını uygular. Tek tek denetleyiciler için geri alma yöntemi yoktur. Derleme düzeyi özniteliğini `Startup` sınıfı çevreleyen ad alanı bildirimine uygulayın:

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

Öznitelik `[ApiController]` , öznitelik yönlendirme bir gereksinim yapar. Örneğin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Eylemlere `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> veya içinde `Startup.Configure`tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla erişilemez.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Eylemlere, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> veya <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> içinde `Startup.Configure`tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla erişilemez.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Otomatik HTTP 400 yanıtları

Öznitelik `[ApiController]` , model doğrulama hatalarının otomatik olarak bir HTTP 400 yanıtı tetiklenmesine neden olur. Sonuç olarak, aşağıdaki kod bir eylem yönteminde gereksizdir:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC, önceki <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> denetimi yapmak için eylem filtresini kullanır.

### <a name="default-badrequest-response"></a>Varsayılan BadRequest yanıtı

Uyumluluk sürümü 2,1 ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.SerializableError>. Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

2,2 veya üzeri bir uyumluluk sürümü ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:

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

`ValidationProblemDetails` Tür:

* Web API yanıtlarında hata belirtmek için makine tarafından okunabilen bir biçim sağlar.
* [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807)uyar.

::: moniker-end

### <a name="log-automatic-400-responses"></a>Otomatik 400 yanıtlarını günlüğe kaydet

Bkz. [otomatik 400 yanıtlarını model doğrulama hatalarında günlüğe kaydetme (ASPNET/AspNetCore. Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Otomatik 400 yanıtını devre dışı bırak

Otomatik 400 davranışını devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> özelliğini olarak `true`ayarlayın. Aşağıdaki Vurgulanan kodu içine `Startup.ConfigureServices`ekleyin:

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
> Değerler ( `[FromRoute]` yani `%2f` `/`) içerdiğinde kullanmayın. `%2f`atlanmaz `/`. Değer `[FromQuery]` içermesi `%2f`gerekiyorsa kullanın.

`[ApiController]` Özniteliği veya gibi `[FromQuery]`bağlama kaynak öznitelikleri olmadan, ASP.NET Core çalışma zamanı karmaşık nesne modeli cildi kullanmaya çalışır. Karmaşık nesne modeli Ciltçi, verileri değer sağlayıcılarından tanımlı bir düzende çeker.

Aşağıdaki örnekte `[FromQuery]` öznitelik, istek URL 'sinin sorgu dizesinde `discontinuedOnly` parametre değerinin sağlandığını gösterir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` Öznitelik, eylem parametrelerinin varsayılan veri kaynakları için çıkarım kurallarını uygular. Bu kurallar, eylem parametrelerine öznitelikleri uygulayarak bağlama kaynaklarını el ile tanımlamak zorunda kalmadan sizi kaydeder. Bağlama kaynak çıkarımı kuralları aşağıdaki gibi davranır:

* `[FromBody]`karmaşık tür parametreleri için algılanır. `[FromBody]` Çıkarım kuralı için bir özel durum, <xref:Microsoft.AspNetCore.Http.IFormCollection> ve <xref:System.Threading.CancellationToken>gibi özel bir anlamı olan karmaşık, yerleşik bir türdür. Bağlama kaynak çıkarımı kodu bu özel türleri yoksayar.
* `[FromForm]`, ve <xref:Microsoft.AspNetCore.Http.IFormFile> <xref:Microsoft.AspNetCore.Http.IFormFileCollection>türündeki eylem parametreleri için algılanır. Bu, herhangi bir basit veya Kullanıcı tanımlı tür için çıkarsanamıyor.
* `[FromRoute]`yol şablonundaki bir parametreyle eşleşen herhangi bir eylem parametresi adı için algılanır. Birden fazla yol bir eylem parametresiyle eşleştiğinde, herhangi bir rota değeri kabul `[FromRoute]`edilir.
* `[FromQuery]`diğer eylem parametreleri için algılanır.

### <a name="frombody-inference-notes"></a>FromBody çıkarım notları

`[FromBody]`, `string` veya `int`gibi basit türler için çıkarsanamıyor. Bu nedenle, `[FromBody]` bu işlev gerektiğinde basit türler için özniteliği kullanılmalıdır.

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
> ASP.NET Core 2,1 ' de, listeler ve diziler gibi koleksiyon türü parametreleri yanlış olarak `[FromQuery]`algılanır. `[FromBody]` Öznitelik, istek gövdesinden bağlanmaları durumunda bu parametreler için kullanılmalıdır. Bu davranış ASP.NET Core 2,2 veya sonraki bir sürümde düzeltilir. burada, koleksiyon türü parametrelerinin varsayılan olarak gövdeden bağlanacak şekilde çıkarsandır.

::: moniker-end

### <a name="disable-inference-rules"></a>Çıkarım kurallarını devre dışı bırak

Bağlama kaynak çıkarımını devre dışı bırakmak <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> için `true`, olarak ayarlayın. Aşağıdaki kodu içine `Startup.ConfigureServices`ekleyin:

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

Özniteliği `[ApiController]` ile [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) bir eylem parametresine ek açıklama eklendiğinde öznitelik bir çıkarım kuralı uygular. `multipart/form-data` İstek içerik türü çıkarsandı.

Varsayılan davranışı devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> özelliğini `true` olarak `Startup.ConfigureServices`ayarlayın:

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

Uyumluluk sürümü 2,2 veya üzeri olduğunda, MVC bir hata sonucunu (durum kodu 400 veya üzeri olan bir sonuç) ile <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>bir sonuçla dönüştürür. `ProblemDetails` Türü, bir http yanıtında makine tarafından okunabilen hata ayrıntılarını sağlamak için [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807) dayalıdır.

Bir denetleyici eyleminde aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Yöntemi `NotFound` , `ProblemDetails` gövdesi olan bir HTTP 404 durum kodu üretir. Örneğin:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>ProblemDetails yanıtını devre dışı bırak

<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> Özelliği olarak `true`ayarlandığında hata durumu kodlarının otomatik olarak `ProblemDetails` oluşturulması devre dışı bırakılır. Aşağıdaki kodu içine `Startup.ConfigureServices`ekleyin:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Desteklenen istek içerik türlerini [tüketir] özniteliğiyle tanımlayın

Varsayılan olarak, bir eylem tüm kullanılabilir istek içerik türlerini destekler. Örneğin, bir uygulama hem JSON hem de XML [giriş formatlarını](xref:mvc/models/model-binding#input-formatters)destekleyecek şekilde yapılandırıldıysa, bir eylem, ve `application/json` `application/xml`dahil olmak üzere birden çok içerik türünü destekler.

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar. `[Consumes]` Özniteliği bir eylem veya denetleyiciye uygulayarak bir veya daha fazla içerik türü belirtin:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

Yukarıdaki kodda, `CreateProduct` eylem içerik türünü `application/xml`belirtir. Bu eyleme yönlendirilen isteklerin bir `Content-Type` üst bilgisi belirtmesi gerekir. `application/xml` `Content-Type` Üst bilgisi belirtmeyen istekler, `application/xml` [415 desteklenmeyen medya türü](https://developer.mozilla.org/docs/Web/HTTP/Status/415) yanıtı ile sonuçlanır.

`[Consumes]` Öznitelik Ayrıca bir eylemin bir tür kısıtlaması uygulayarak bir gelen isteğin içerik türüne göre seçimini etkilemesini sağlar. Aşağıdaki örneği inceleyin:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

Önceki kodda, `ConsumesController` `https://localhost:5001/api/Consumes` URL 'ye gönderilen istekleri işleyecek şekilde yapılandırılır. Denetleyicinin eylemlerinin `PostJson` `PostForm`her ikisi de aynı URL 'ye sahip post isteklerini işler. `[Consumes]` Öznitelik bir tür kısıtlaması uygulamadan, belirsiz eşleşme özel durumu oluşur.

`[Consumes]` Özniteliği her iki eyleme de uygulanır. Eylem `PostJson` , `Content-Type` üst bilgisi ile gönderilen istekleri işler `application/json`. Eylem `PostForm` , `Content-Type` üst bilgisi ile gönderilen istekleri işler `application/x-www-form-urlencoded`. 

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
