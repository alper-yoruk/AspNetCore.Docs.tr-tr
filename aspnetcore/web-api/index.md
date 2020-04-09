---
title: ASP.NET Core ile web API'leri oluşturma
author: scottaddie
description: ASP.NET Core'da web API oluşturmanın temellerini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666007"
---
# <a name="create-web-apis-with-aspnet-core"></a>ASP.NET Core ile web API'leri oluşturma

Scott [Addie](https://github.com/scottaddie) ve [Tom Dykstra](https://github.com/tdykstra) tarafından

ASP.NET Core, web API'leri olarak da bilinen RESTful hizmetleri oluşturulmasını C# kullanarak desteklemektedir. İstekleri işlemek için web API denetleyicileri kullanır. Web API'sindeki *denetleyiciler,* `ControllerBase`'den türeyen sınıflardır. Bu makalede, web API isteklerini işlemek için denetleyicileri nasıl kullanılacağı nı gösterir.

[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples) ([Nasıl indirilir).](xref:index#how-to-download-a-sample)

## <a name="controllerbase-class"></a>ControllerBase sınıfı

Web API'si, bir veya daha <xref:Microsoft.AspNetCore.Mvc.ControllerBase>fazla denetleyici sınıftan oluşur. Web API proje şablonu bir başlatıcı denetleyicisi sağlar:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<xref:Microsoft.AspNetCore.Mvc.Controller> Sınıftan çıkararak bir web API denetleyicisi oluşturmayın. `Controller`görünümlerden kaynaklanır `ControllerBase` ve görünümler için destek ekler, bu nedenle web API isteklerini değil, web sayfalarını işlemek içindir. Bu kuralın bir istisnası vardır: Hem görünümler hem de web API'leri `Controller`için aynı denetleyiciyi kullanmayı planlıyorsanız, bunu .

Sınıf, `ControllerBase` HTTP isteklerini işlemek için yararlı olan birçok özellik ve yöntem sağlar. Örneğin, `ControllerBase.CreatedAtAction` 201 durum kodunu döndürür:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Aşağıda, `ControllerBase` sağlayan yöntemlere birkaç örnek daha verilmiştir.

|Yöntem   |Notlar    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| 400 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|404 durum kodunu döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Bir dosyayı döndürür.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Model [bağlamayı](xref:mvc/models/model-binding)çağırır.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Model [doğrulaması](xref:mvc/models/validation)çağırır.|

Kullanılabilir tüm yöntem ve özelliklerin <xref:Microsoft.AspNetCore.Mvc.ControllerBase>listesi için bkz.

## <a name="attributes"></a>Öznitelikler

Ad <xref:Microsoft.AspNetCore.Mvc> alanı, web API denetleyicilerinin ve eylem yöntemlerinin davranışını yapılandırmak için kullanılabilecek öznitelikler sağlar. Aşağıdaki örnek, desteklenen HTTP eylem fiilini ve döndürülebilecek bilinen herhangi bir HTTP durum kodlarını belirtmek için öznitelikleri kullanır:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Burada kullanılabilir özniteliklere birkaç daha fazla örnek verilmiştir.

|Öznitelik|Notlar|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Denetleyici veya eylem için URL deseni belirtir.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Model bağlama için ekecek önek ve özellikler belirtir.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |HTTP GET eylem fiilini destekleyen bir eylemi tanımlar.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Bir eylemin kabul ettiği veri türlerini belirtir.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Bir eylemin döndürdüğü veri düşüntülerini|

Kullanılabilir öznitelikleri içeren bir liste <xref:Microsoft.AspNetCore.Mvc> için ad alanına bakın.

## <a name="apicontroller-attribute"></a>ApiController özniteliği

Öznitelik, [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) aşağıdaki göreli, API'ye özgü davranışları etkinleştirmek için bir denetleyici sınıfına uygulanabilir:

::: moniker range=">= aspnetcore-2.2"

* [Öznitelik yönlendirme gereksinimi](#attribute-routing-requirement)
* [Otomatik HTTP 400 yanıtları](#automatic-http-400-responses)
* [Bağlayıcı kaynak parametresi çıkarımı](#binding-source-parameter-inference)
* [Çoklu bölüm/form-veri isteği çıkarımı](#multipartform-data-request-inference)
* [Hata durum kodları için sorun ayrıntıları](#problem-details-for-error-status-codes)

Hata durumu kodları özelliği *için Sorun ayrıntıları* 2.2 veya daha sonra bir uyumluluk [sürümü](xref:mvc/compatibility-version) gerektirir. Diğer özellikler 2.1 veya sonraki bir uyumluluk sürümü gerektirir.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Öznitelik yönlendirme gereksinimi](#attribute-routing-requirement)
* [Otomatik HTTP 400 yanıtları](#automatic-http-400-responses)
* [Bağlayıcı kaynak parametresi çıkarımı](#binding-source-parameter-inference)
* [Çoklu bölüm/form-veri isteği çıkarımı](#multipartform-data-request-inference)

Bu özellikler 2.1 veya sonraki bir [uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Belirli denetleyicilere öznitelik

Öznitelik, `[ApiController]` proje şablonundaki aşağıdaki örnekte olduğu gibi belirli denetleyicilere uygulanabilir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Birden çok denetleyiciye öznitelik

Özniteliği birden fazla denetleyicide kullanmaya yönelik bir yaklaşım, öznitelik ile `[ApiController]` açıklamalı özel bir taban denetleyici sınıfı oluşturmaktır. Aşağıdaki örnek, özel bir taban sınıf ve ondan türetilen bir denetleyici gösterir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Montajda öznitelik

[Uyumluluk sürümü](xref:mvc/compatibility-version) 2.2 veya daha sonra `[ApiController]` olarak ayarlanırsa, öznitelik bir derlemeye uygulanabilir. Bu şekilde ek açıklama, derlemedeki tüm denetleyicilere web API davranışını uygular. Tek tek denetleyicileri devre dışı bırakmanın bir yolu yok. Sınıfı çevreleyen ad alanı bildirimine derleme düzeyinde `Startup` öznitelik uygulayın:

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

Öznitelik `[ApiController]` öznitelik yönlendirme bir gereksinim i Örneğin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

[conventional routes](xref:mvc/controllers/routing#conventional-routing) Eylemlere `UseEndpoints`, ' <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>veya <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> . `Startup.Configure`

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Eylemlere, tarafından <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> tanımlanan geleneksel <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> [yollarla](xref:mvc/controllers/routing#conventional-routing) veya `Startup.Configure`

::: moniker-end

## <a name="automatic-http-400-responses"></a>Otomatik HTTP 400 yanıtları

Öznitelik, model doğrulama hatalarının `[ApiController]` otomatik olarak bir HTTP 400 yanıtını tetikletir. Sonuç olarak, bir eylem yönteminde aşağıdaki kod gereksizdir:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> önceki denetimi yapmak için eylem filtresini kullanır.

### <a name="default-badrequest-response"></a>Varsayılan BadRequest yanıtı

2.1 uyumluluk sürümüile, bir HTTP 400 yanıtı için <xref:Microsoft.AspNetCore.Mvc.SerializableError>varsayılan yanıt türü . Aşağıdaki istek gövdesi serileştirilmiş türe örnektir:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

2.2 veya sonraki uyumluluk sürümünde, HTTP 400 yanıtı için <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>varsayılan yanıt türü . Aşağıdaki istek gövdesi serileştirilmiş türe örnektir:

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

Türü: `ValidationProblemDetails`

* Web API yanıtlarında hataları belirtmek için makine tarafından okunabilir bir biçim sağlar.
* [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807)uygundur.

::: moniker-end

### <a name="log-automatic-400-responses"></a>Günlük otomatik 400 yanıt

[Model doğrulama hataları (aspnet/AspNetCore.Docs #12157) üzerinde otomatik 400 yanıtları nasıl günlüğe kaydedeceğimi](https://github.com/dotnet/AspNetCore.Docs/issues/12157)görün.

### <a name="disable-automatic-400-response"></a>Otomatik 400 yanıtını devre dışı

Otomatik 400 davranışını devre dışı <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> bırakabilmek için `true`özelliği ' ye göre ayarla Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Bağlayıcı kaynak parametresi çıkarımı

Bağlayıcı kaynak özniteliği, eylem parametresi değerinin bulunduğu konumu tanımlar. Aşağıdaki bağlayıcı kaynak öznitelikleri vardır:

|Öznitelik|Kaynak bağlama |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | İstek gövdesi |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | İstek gövdesindeki form verileri |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | İstek üst bilgisi |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | İstek sorgusu dize parametresi |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Geçerli istekteki verileri yönlendirme |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Eylem parametresi olarak enjekte edilen istek hizmeti |

> [!WARNING]
> Değerler içerebileceğinde `[FromRoute]` `%2f` `/`(yani) kullanmayın. `%2f`kaçmadan `/`olmayacaktır. Değer `[FromQuery]` içeriyorsa `%2f`kullanın.

Gibi `[ApiController]` öznitelik veya bağlayıcı `[FromQuery]`kaynak öznitelikleri olmadan, ASP.NET Core çalışma zamanı karmaşık nesne modeli bağlayıcısı kullanmaya çalışır. Karmaşık nesne modeli bağlayıcısı, tanımlı bir sırada değer sağlayıcılardan veri çeker.

Aşağıdaki örnekte, `[FromQuery]` `discontinuedOnly` öznitelik, parametre değerinin istek URL'sinin sorgu dizesinde sağlandığını gösterir:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Öznitelik, `[ApiController]` eylem parametrelerinin varsayılan veri kaynakları için çıkarım kuralları uygular. Bu kurallar, eylem parametrelerine öznitelikleri uygulayarak bağlayıcı kaynakları el ile tanımlamak zorunda kalmanızı sağlar. Bağlayıcı kaynak çıkarım kuralları aşağıdaki gibi hareket eder:

* `[FromBody]`karmaşık tür parametreleri için çıkarılır. Çıkarım kuralının `[FromBody]` bir istisnası, özel bir anlamı olan karmaşık, <xref:Microsoft.AspNetCore.Http.IFormCollection> yerleşik <xref:System.Threading.CancellationToken>bir türdür. Bağlayıcı kaynak çıkarım kodu bu özel türleri yoksayılsın.
* `[FromForm]`türü <xref:Microsoft.AspNetCore.Http.IFormFile> ve <xref:Microsoft.AspNetCore.Http.IFormFileCollection>eylem parametreleri için çıkarılır. Basit veya kullanıcı tarafından tanımlanan türler için çıkarılmaz.
* `[FromRoute]`rota şablonundaki bir parametreyle eşleşen herhangi bir eylem parametresi adı için çıkarılır. Birden fazla rota bir eylem parametresi ile `[FromRoute]`eşleştiğinde, herhangi bir rota değeri dikkate alınr.
* `[FromQuery]`diğer eylem parametreleri için çıkarılır.

### <a name="frombody-inference-notes"></a>FromBody çıkarım notları

`[FromBody]`gibi basit türleri için `string` çıkarılmaz. `int` Bu nedenle, `[FromBody]` öznitelik bu işlevsellik gerektiğinde basit türleri için kullanılmalıdır.

Bir eylemin istek gövdesinden birden fazla parametre bağlı olması halinde, bir özel durum atılır. Örneğin, aşağıdaki eylem yöntemi imzalarının tümü bir özel durum neden olur:

* `[FromBody]`karmaşık türleri olduğu için her ikisi de çıkarılır.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]`karmaşık bir tür olduğu için diğerinde çıkarılan öznitelik.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]`her ikide de öznitelik.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> ASP.NET Core 2.1'de, listeler ve diziler gibi toplama türü `[FromQuery]`parametreleri yanlış olarak çıkarılır. İstek `[FromBody]` gövdesinden bağlı olacaksa, bu parametreler için öznitelik kullanılmalıdır. Bu davranış, toplama türü parametrelerinin varsayılan olarak gövdeden bağlı olarak çıkarıldığı ASP.NET Core 2.2 veya daha sonra düzeltilir.

::: moniker-end

### <a name="disable-inference-rules"></a>Çıkarım kurallarını devre dışı

Bağlayıcı kaynak çıkarımLarını devre dışı <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> `true`kılabilir, '' olarak ayarlayın Aşağıdaki kodu `Startup.ConfigureServices`ekleyin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Çoklu bölüm/form-veri isteği çıkarımı

Bir `[ApiController]` eylem parametresi öznitelik ile [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) açıklamalı olduğunda öznitelik bir çıkarım kuralı uygular. İstek `multipart/form-data` içeriği türü çıkarılır.

Varsayılan davranışı devre dışı kılabilir, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> `true` özelliği `Startup.ConfigureServices`şu şekilde ayarlayın:

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

Uyumluluk sürümü 2,2 veya daha sonra olduğunda, MVC bir hata sonucunu (durum kodu 400 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>veya daha yüksek bir sonuç) . Tür, `ProblemDetails` http yanıtında makine tarafından okunabilir hata ayrıntıları sağlamak için [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807) dayanır.

Denetleyici eyleminde aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Yöntem, `NotFound` gövdeli bir `ProblemDetails` HTTP 404 durum kodu üretir. Örneğin:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>SorunAyrıntıları devre dışı

Hata durum kodları `ProblemDetails` için otomatik oluşturma <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> özelliği ayarlandığında `true`devre dışı bırakılır. Aşağıdaki kodu `Startup.ConfigureServices`ekleyin:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Desteklenen istek içerik türlerini [Tüketenler] özelliğiyle tanımlama

Varsayılan olarak, bir eylem tüm kullanılabilir istek içerik türlerini destekler. Örneğin, bir uygulama hem JSON hem de XML [giriş formatters](xref:mvc/models/model-binding#input-formatters)destekleyecek şekilde yapılandırılırsa, bir eylem dahil olmak üzere birden çok içerik türünü `application/json` destekler. `application/xml`

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlamak için bir eyleme izin verir. `[Consumes]` Bir veya daha fazla içerik türünü belirterek bir eyleme veya denetleyiciye özniteliği uygulayın:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

Önceki kodda, `CreateProduct` eylem içerik türünü `application/xml`belirtir. Bu eyleme yönlendirilen `Content-Type` istekler `application/xml`bir üstbilgi belirtmelidir. [415 Desteklenmeyen Medya](https://developer.mozilla.org/docs/Web/HTTP/Status/415) `application/xml` Türü yanıtında bir `Content-Type` üstbilgi belirtmeyan istekler.

Öznitelik `[Consumes]` ayrıca, bir tür kısıtlaması uygulayarak gelen bir isteğin içerik türüne dayalı olarak bir eylemin seçimini etkilemesine de olanak tanır. Aşağıdaki örneği inceleyin:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

Önceki kodda, `ConsumesController` `https://localhost:5001/api/Consumes` URL'ye gönderilen istekleri işlemek için yapılandırılmıştır. Denetleyicinin eylemlerinin her `PostJson` ikisi `PostForm`de ve POST isteklerini aynı URL ile işler. `[Consumes]` Öznitelik bir tür kısıtlaması uygulamadan, belirsiz bir eşleme özel durumu atılır.

Öznitelik `[Consumes]` her iki eyleme de uygulanır. Eylem, `PostJson` üstbilgi `Content-Type` ile `application/json`gönderilen istekleri işler. Eylem, `PostForm` üstbilgi `Content-Type` ile `application/x-www-form-urlencoded`gönderilen istekleri işler. 

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
