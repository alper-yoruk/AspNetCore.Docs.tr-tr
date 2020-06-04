---
Başlık: ASP.NET Core Web API yazarındaki yanıt verilerini biçimlendirme: ardalış açıklaması: ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirmeyi öğrenin.
MS. Author: Riande MS. Custom: H1Hack27Feb2017 MS. Date: 04/17/2020 No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: Web-API/gelişmiş/biçimlendirme

---
# <a name="format-response-data-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirme

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC, yanıt verilerini biçimlendirme desteğine sahiptir. Yanıt verileri, belirli biçimler kullanılarak veya istemci tarafından istenen biçime yanıt olarak biçimlendirilebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Formata özgü eylem sonuçları

Bazı eylem sonuç türleri, ve gibi belirli bir biçime özgüdür <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult> . Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilen sonuçları döndürebilir. Örneğin, döndürme `JsonResult` JSON biçimli verileri döndürür. Döndürme `ContentResult` veya dize, düz metin biçimli dize verileri döndürür.

Belirli bir tür döndürmek için bir eylem gerekli değildir. ASP.NET Core, tüm nesne dönüş değerlerini destekler.  Tür olmayan nesneleri döndüren eylemlerin sonuçları <xref:Microsoft.AspNetCore.Mvc.IActionResult> , uygun uygulama kullanılarak serileştirilir <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> . Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.

Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimli verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Örnek indirme, yazarların listesini döndürür. Önceki kodla F12 tarayıcı geliştirici araçları veya [Postman](https://www.getpostman.com/tools) kullanma:

* **Content-Type:** içeren yanıt üst bilgisi `application/json; charset=utf-8` görüntülenir.
* İstek üst bilgileri görüntülenir. Örneğin, `Accept` üst bilgi. `Accept`Üst bilgi, önceki kod tarafından yok sayılır.

Düz metin biçimli verileri döndürmek için <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> ve <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> yardımcısını kullanın:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Yukarıdaki kodda, `Content-Type` döndürülen `text/plain` . Şu şekilde bir dize `Content-Type` döndürür `text/plain` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Birden çok dönüş türüne sahip eylemler için, döndürün `IActionResult` . Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürülüyor.

## <a name="content-negotiation"></a>İçerik anlaşması

İstemci bir [Accept üst bilgisi](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde içerik anlaşması oluşur. ASP.NET Core tarafından kullanılan varsayılan biçim [JSON](https://json.org/)'dir. İçerik anlaşması:

* Uygulayan <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .
* Yardımcı metotlarından döndürülen durum koduna özgü eylem sonuçlarına yerleşik olarak. Eylem sonuçları yardımcı yöntemleri temel alınır `ObjectResult` .

Bir model türü döndürüldüğünde, dönüş türü olur `ObjectResult` .

Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemlerini kullanır:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Varsayılan olarak, ASP.NET Core `application/json` , `text/json` ve `text/plain` medya türlerini destekler. [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, `Accept` istek üst bilgisini dönüş biçimini belirtecek şekilde ayarlayabilir. `Accept`Üst bilgi, sunucunun desteklediği bir tür içerdiğinde, bu tür döndürülür. Sonraki bölümde, ek Biçimlendiriciler ekleme gösterilmektedir.

Denetleyici eylemleri POCOs (düz eski CLR nesneleri) döndürebilir. POCO döndürüldüğünde, çalışma zamanı nesneyi sarmalayan bir otomatik olarak oluşturur `ObjectResult` . İstemci, biçimlendirilen serileştirilmiş nesneyi alır. Döndürülen nesne ise `null` `204 No Content` yanıt döndürülür.

Nesne türü döndürülüyor:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Önceki kodda, geçerli bir yazar diğer adı için bir istek `200 OK` yazarın verileriyle bir yanıt döndürür. Geçersiz bir diğer ad isteği bir yanıt döndürüyor `204 No Content` .

### <a name="the-accept-header"></a>Accept üst bilgisi

İstekte *negotiation* bir `Accept` üst bilgi göründüğünde içerik anlaşması gerçekleşir. Bir istek bir Accept üst bilgisi içerdiğinde ASP.NET Core:

* Kabul üst bilgisindeki medya türlerini tercih sırasına göre numaralandırır.
* Belirtilen biçimlerden birinde yanıt üretemeyen bir biçimlendirici bulmaya çalışır.

İstemcinin isteğini karşılayabilen bir biçimlendirici bulunmazsa ASP.NET Core:

* `406 Not Acceptable`Ayarlanmışsa döndürür <xref:Microsoft.AspNetCore.Mvc.MvcOptions> veya-
* Yanıt üreten ilk biçimlendirici bulmayı dener.

İstenen biçim için bir biçimlendirici yapılandırılmamışsa, nesneyi biçimlendirebileceğini ilk biçimlendirici kullanılır. İstekte hiçbir `Accept` başlık görünürse:

* Nesneyi işleyebilen ilk biçimlendirici, yanıtı seri hale getirmek için kullanılır.
* Hiçbir anlaşma gerçekleşmiyor. Sunucu hangi biçimin döneceğine karar verir.

Accept üst bilgisi içeriyorsa `*/*` üstbilgi, `RespectBrowserAcceptHeader` true olarak ayarlanmadığı müddetçe yok sayılır <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

### <a name="browsers-and-content-negotiation"></a>Tarayıcılar ve içerik anlaşması

Tipik API istemcilerinin aksine, Web tarayıcıları `Accept` üst bilgileri sağlar. Web tarayıcısı, joker karakterler dahil olmak üzere birçok biçim belirtir. Varsayılan olarak, Framework isteğin bir tarayıcıdan geldiğini algıladığında:

* `Accept`Üst bilgi yok sayılır.
* Aksi yapılandırılmadığı takdirde içerik JSON içinde döndürülür.

Bu, API 'Leri tükettiren tarayıcılarda daha tutarlı bir deneyim sağlar.

Bir uygulamayı tarayıcı onay üstbilgilerini kabul edecek şekilde yapılandırmak için, şu <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> şekilde ayarlayın `true` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Biçimleri yapılandırma

Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir. Giriş ve çıkış için ayrı biçimlendirme vardır. Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır. Çıkış biçimleri, yanıtları biçimlendirmek için kullanılır. Özel bir biçimlendirici oluşturma hakkında daha fazla bilgi için bkz. [özel Formatlayıcılar](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>XML biçimi desteği ekle

Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .

Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürür `Accept` .

### <a name="configure-systemtextjson-based-formatters"></a>System. Text. JSON tabanlı formatlayıcıları yapılandırma

`System.Text.Json`Tabanlı formatlayıcılar için özellikler kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` . Örnek:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Newtonsoft. JSON tabanlı JSON biçimi desteği ekleyin

ASP.NET Core 3,0 ' dan önce, varsayılan olarak kullanılan JSON formatlayıcıları paket kullanılarak uygulanır `Newtonsoft.Json` . ASP.NET Core 3,0 veya sonraki bir sürümde, varsayılan JSON formatlayıcıları temel alınır `System.Text.Json` . `Newtonsoft.Json`Temel biçimlendirme ve özellikler için destek, [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketi yüklenerek ve içinde yapılandırılarak kullanılabilir `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Bazı özellikler `System.Text.Json` , tabanlı formatlayıcılar ile düzgün çalışmayabilir ve `Newtonsoft.Json` tabanlı formatlara bir başvuru gerektirir. `Newtonsoft.Json`Uygulama şu durumlarda tabanlı formatlayıcıları kullanmaya devam edin:

* `Newtonsoft.Json`Öznitelikleri kullanır. Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.
* Serileştirme ayarlarını özelleştirir.
* , Tarafından sağlanan özellikleri kullanır `Newtonsoft.Json` .
* Yapılandırır `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` . ASP.NET Core 3,0 ' dan önce, `JsonResult.SerializerSettings` öğesine özgü bir örneğini kabul eder `JsonSerializerSettings` `Newtonsoft.Json` .
* [Openapı](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.

`Newtonsoft.Json`Tabanlı formatlayıcılar için özellikler şu kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` . Örnek:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>XML biçimi desteği ekle

XML biçimlendirme, [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketini gerektirir.

Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .

Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürmelidir `Accept` .

::: moniker-end

### <a name="specify-a-format"></a>Biçim belirtin

Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın. Çoğu [filtre](xref:mvc/controllers/filters)gibi `[Produces]` eylem, denetleyici veya genel kapsamda de uygulanabilir:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:

* Denetleyici içindeki tüm eylemleri JSON biçimli yanıtları döndürecek şekilde zorlar.
* Diğer formatlayıcılar yapılandırıldıysa ve istemci farklı bir biçim belirtiyorsa JSON döndürülür.

Daha fazla bilgi için bkz. [Filtreler](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Özel durum formatları

Bazı özel durumlar, yerleşik formatlayıcılar kullanılarak uygulanır. Varsayılan olarak, `string` dönüş türleri *metin/düz* olarak biçimlendirilir (üst bilgi ile isteniyorsa*metin/html* `Accept` ). Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> . Biçimlendiriciler `ConfigureServices` yönteminde kaldırılır. Bir model nesne dönüş türü döndürme sırasında döndürülen eylemler `204 No Content` `null` . Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> . Aşağıdaki kod ve öğesini kaldırır `StringOutputFormatter` `HttpNoContentOutputFormatter` .

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Olmadan `StringOutputFormatter` , YERLEŞIK JSON biçimlendiricisi, `string` dönüş türlerini biçimlendirir. Yerleşik JSON biçimlendiricisi kaldırılırsa ve bir XML biçimlendirici varsa, XML biçimlendirici, `string` dönüş türlerini biçimlendirir. Aksi takdirde, `string` dönüş türleri döndürülür `406 Not Acceptable` .

Olmadan `HttpNoContentOutputFormatter` , null nesneler yapılandırılmış biçimlendirici kullanılarak biçimlendirilir. Örnek:

* JSON biçimlendiricisi, gövdesi olan bir yanıt döndürür `null` .
* XML biçimlendiricisi özniteliği ayarlanmış bir boş XML öğesi döndürüyor `xsi:nil="true"` .

## <a name="response-format-url-mappings"></a>Yanıt biçimi URL eşlemeleri

İstemciler URL 'nin bir parçası olarak belirli bir biçim talep edebilir, örneğin:

* Sorgu dizesinde veya yolun bir bölümünde.
* . Xml veya. JSON gibi formata özgü bir dosya uzantısı kullanarak.

İstek yolundan eşleme, API 'nin kullandığı rotada belirtilmelidir. Örnek:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Önceki yol, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesini sağlar. [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Özniteliği, içindeki biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun biçimlendirici ile eşler.

|           Yol        |             Biçimlendirici              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Varsayılan çıkış biçimlendiricisi    |
| `/api/products/5.json` | JSON biçimlendiricisi (yapılandırıldıysa) |
| `/api/products/5.xml`  | XML biçimlendiricisi (yapılandırıldıysa)  |
