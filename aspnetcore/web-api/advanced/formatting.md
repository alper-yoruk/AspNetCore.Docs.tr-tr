---
title: Yanıt verilerini ASP.NET Çekirdek Web API'sinde biçimlendirme
author: ardalis
description: Yanıt verilerini ASP.NET Core Web API'de nasıl biçimlendireceklerini öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642705"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Yanıt verilerini ASP.NET Çekirdek Web API'sinde biçimlendirme

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC yanıt verilerini biçimlendirme desteğine sahiptir. Yanıt verileri belirli biçimler kullanılarak veya istemcinin istediği biçime yanıt olarak biçimlendirilebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Biçime Özel Eylem Sonuçları

Bazı eylem sonuç türleri belirli bir biçime özgü, gibi <xref:Microsoft.AspNetCore.Mvc.JsonResult> ve <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilmiş sonuçlar döndürebilir. Örneğin, json `JsonResult` biçimlendirilmiş verileri döndüren döndürür. İade `ContentResult` veya dize düz metin biçimlendirilmiş dize verilerini döndürür.

Belirli bir türü döndürmek için bir eylem gerekmez. ASP.NET Core herhangi bir nesne iade değerini destekler.  Türü olmayan <xref:Microsoft.AspNetCore.Mvc.IActionResult> nesneleri döndüren eylemlerin sonuçları, uygun <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> uygulama kullanılarak seri olarak işlenir. Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.

Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimlendirilmiş verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Örnek karşıdan yükleme, yazar listesini döndürür. Önceki kod ile F12 tarayıcı geliştirici araçları veya [Postacı](https://www.getpostman.com/tools) kullanma:

* **İçerik türünü** `application/json; charset=utf-8` içeren yanıt üstbilgi: görüntülenir.
* İstek üstbilgisi görüntülenir. Örneğin, `Accept` üstbilgi. Üstbilgi `Accept` önceki kod tarafından yoksayılır.

Düz metin biçimlendirilmiş verileri <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> döndürmek <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> için, kullanımı ve yardımcısı:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Önceki kodda, `Content-Type` `text/plain`döndürülen. Bir dize `Content-Type` döndürme `text/plain`sunar:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Birden çok iade türüne `IActionResult`sahip eylemler için return. Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürme.

## <a name="content-negotiation"></a>İçerik anlaşması

İçerik anlaşması, istemci bir Kabul [üstbilgisini](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde oluşur. ASP.NET Core tarafından kullanılan varsayılan biçim [JSON'dur.](https://json.org/) İçerik pazarlığı:

* tarafından <xref:Microsoft.AspNetCore.Mvc.ObjectResult>uygulanır.
* Yardımcı yöntemlerden döndürülen durum koduna özgü eylem sonuçları yerleşiktir. Eylem sonuçları yardımcı yöntemleri dayanır. `ObjectResult`

Bir model türü döndürüldüğünde, iade `ObjectResult`türü .

Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemleri kullanır:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Varsayılan olarak, ASP.NET `application/json` `text/json`Core `text/plain` destekler , ve medya türleri. [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, iade biçimini belirtmek için istek üstbilgisini `Accept` ayarlayabilir. `Accept` Üstbilgi sunucunun desteklediği bir tür içeriyorsa, bu tür döndürülür. Sonraki bölümde ek formatters nasıl ekleyeceğinigösterir.

Denetleyici eylemleri POCOs (Düz Eski CLR Nesneleri) döndürebilir. Bir POCO döndürüldüğünde, çalışma zamanı nesneyi `ObjectResult` saran bir otomatik olarak oluşturur. İstemci biçimlendirilmiş serileştirilmiş nesneyi alır. Döndürülen nesne ise, `null` `204 No Content` yanıt döndürülür.

Nesne türünü döndürme:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Önceki kodda, geçerli bir yazar takma adı `200 OK` için bir istek yazarın verileriyle bir yanıt döndürür. Geçersiz bir takma ad isteği `204 No Content` yanıtı döndürür.

### <a name="the-accept-header"></a>Kabul başlığı

İçerik *anlaşması,* istekte bir `Accept` üstbilgi göründüğünde gerçekleşir. Bir istek kabul üstbilgisi içeriyorsa, ASP.NET Core:

* Tercih sırasına göre kabul üstbilgisinde ortam türlerini sıralar.
* Belirtilen biçimlerden birinde yanıt üretebilecek bir madde bulmaya çalışır.

Müşterinin isteğini karşılayabilen herhangi bir madde bulunmazsa, ASP.NET Core:

* Ayarlanmışsa `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> döndürür veya -
* Yanıt üretebilecek ilk formatter'ı bulmaya çalışır.

İstenen biçim için hiçbir formatter yapılandırılmamışsa, nesneyi biçimlendirebilen ilk formatter kullanılır. İstekte `Accept` üstbilgi görünmüyorsa:

* Nesneyi işleyebilir ilk formatter yanıtı serileştirmek için kullanılır.
* Herhangi bir pazarlık falan yok. Sunucu hangi biçimin döndürülecek olduğunu belirliyor.

Üstbilgi kabul içeriyorsa, `*/*`üstbilgi `RespectBrowserAcceptHeader` üzerinde doğru ayarlanmadığı sürece <xref:Microsoft.AspNetCore.Mvc.MvcOptions>yoksayılır.

### <a name="browsers-and-content-negotiation"></a>Tarayıcılar ve içerik anlaşması

Tipik API istemcilerinin aksine, `Accept` web tarayıcıları üstbilgi sağlar. Web tarayıcısı joker karakterler de dahil olmak üzere birçok biçimi belirtir. Varsayılan olarak, çerçeve isteğibir tarayıcıdan geldiğini algıladığında:

* Üstbilgi `Accept` yoksayılır.
* İçerik, aksi şekilde yapılandırılmadığı sürece JSON'da döndürülür.

Bu, API'leri tüketirken tarayıcılar arasında daha tutarlı bir deneyim sağlar.

Tarayıcı kabul üstbilgi onurlandırmak için bir <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> uygulama `true`yapılandırmak için, ayarlayın:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Formatters yapılandırılması

Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir. Giriş ve çıkış için ayrı konular vardır. Giriş formatters Model [Bağlama](xref:mvc/models/model-binding)tarafından kullanılır. Çıktı formatters yanıtları biçimlendirmek için kullanılır. Özel bir formatter oluşturma hakkında bilgi için, [Bkz. Özel Formatters.](xref:web-api/advanced/custom-formatters)

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>XML biçim desteği ekleme

XML formatters kullanılarak <xref:System.Xml.Serialization.XmlSerializer> uygulanan çağrı <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>ile yapılandırılır:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Önceki kod kullanarak `XmlSerializer`sonuçları serileştirir.

Önceki kodu kullanırken, denetleyici yöntemleri isteğin `Accept` üstbilgisini temel alan uygun biçimi döndürer.

### <a name="configure-systemtextjson-based-formatters"></a>Configure System.Text.Json tabanlı formatters

`System.Text.Json`-tabanlı formatters için özellikler kullanılarak `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`yapılandırılabilir.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Çıkış serileştirme seçenekleri, eylem başına bazda, kullanılarak `JsonResult`yapılandırılabilir. Örneğin:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Newtonsoft.Json tabanlı JSON format desteği ekle

Core 3.0'ı ASP.NET önce, varsayılan olarak `Newtonsoft.Json` paket kullanılarak uygulanan JSON formatters kullanılmıştır. ASP.NET Core 3.0 veya sonraki olarak, varsayılan JSON formatters `System.Text.Json`dayanmaktadır. [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükleyerek ve .'de `Startup.ConfigureServices`yapılandırarak temel formatters ve özellikler için `Newtonsoft.Json` destek mevcuttur.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Bazı özellikler -tabanlı `System.Text.Json`formatters ile iyi çalışmayabilir `Newtonsoft.Json`ve -tabanlı formatters bir referans gerektirir. Uygulama aşağıdaki `Newtonsoft.Json`gibi esasını kullanmaya devam edin:

* Öznitelikleri kullanır. `Newtonsoft.Json` Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.
* Serileştirme ayarlarını özelleştirin.
* `Newtonsoft.Json` Sağlayan özelliklere dayanır.
* Yapılandırır. `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` Core 3.0ASP.NETden `JsonResult.SerializerSettings` önce, `Newtonsoft.Json`'ye `JsonSerializerSettings` özgü bir örneği kabul eder.
* [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.

`Newtonsoft.Json`-tabanlı formatters için özellikler kullanılarak `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`yapılandırılabilir:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Çıkış serileştirme seçenekleri, eylem başına bazda, kullanılarak `JsonResult`yapılandırılabilir. Örneğin:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>XML biçim desteği ekleme

XML biçimlendirme [microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketi gerektirir.

XML formatters kullanılarak <xref:System.Xml.Serialization.XmlSerializer> uygulanan çağrı <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>ile yapılandırılır:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Önceki kod kullanarak `XmlSerializer`sonuçları serileştirir.

Önceki kodu kullanırken, denetleyici yöntemleri isteğin `Accept` üstbilgisini temel alan uygun biçimi döndürmelidir.

::: moniker-end

### <a name="specify-a-format"></a>Biçim belirtin

Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın. Çoğu [Filtre](xref:mvc/controllers/filters)gibi, `[Produces]` eylem, denetleyici veya genel kapsamda uygulanabilir:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:

* Denetleyici içindeki tüm eylemleri JSON biçimlendirilmiş yanıtları döndürmeye zorlar.
* Diğer formatters yapılandırılır ve istemci farklı bir biçim belirtir, JSON döndürülür.

Daha fazla bilgi için [Bkz. Filtreler.](xref:mvc/controllers/filters)

### <a name="special-case-formatters"></a>Özel durum formatters

Bazı özel durumlar yerleşik formatters kullanılarak uygulanır. Varsayılan `string` olarak, iade türleri *metin/düz* `Accept` (üstbilgi üzerinden istenirse*metin/html)* olarak biçimlendirilir. Bu davranış kaldırArak <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>silinebilir. `ConfigureServices` Formatters yöntemde kaldırılır. Döndürerken model nesnesi `204 No Content` döndürme türü ne zaman döndürülen `null`eylemler. Bu davranış kaldırArak <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>silinebilir. Aşağıdaki kod kaldırır `StringOutputFormatter` ve `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Olmadan `StringOutputFormatter`, dahili JSON formatter `string` biçimleri dönüş türleri. Yerleşik JSON formatter kaldırılır ve bir XML formatter varsa, XML formatter biçimleri `string` döndürme türleri. Aksi `string` takdirde, `406 Not Acceptable`iade türleri döndürün.

Olmadan `HttpNoContentOutputFormatter`, null nesneleri yapılandırılmış formatter kullanılarak biçimlendirilir. Örneğin:

* JSON formatter bir vücut ile `null`bir yanıt döndürür .
* XML formatter öznitelik `xsi:nil="true"` kümesi ile boş bir XML öğesi döndürür.

## <a name="response-format-url-mappings"></a>Yanıt biçimi URL eşlemeleri

İstemciler URL'nin bir parçası olarak belirli bir biçim isteyebilirler, örneğin:

* Sorgu dizesinde veya yolun bir kısmında.
* .xml veya .json gibi biçime özgü bir dosya uzantısı kullanarak.

İstek yolundan eşleme, API'nin kullandığı rotada belirtilmelidir. Örneğin:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Önceki rota, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesine olanak tanır. Öznitelik, [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun maddeyle eşler.

|           Yol        |             Biçimlendiricisi              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Varsayılan çıkış formatter    |
| `/api/products/5.json` | JSON formatter (yapılandırılırsa) |
| `/api/products/5.xml`  | XML formatter (yapılandırılırsa)  |
