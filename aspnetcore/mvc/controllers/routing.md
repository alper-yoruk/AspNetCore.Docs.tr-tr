---
title: ASP.NET Core'daki eylemleri denetleyiciye yönlendirme
author: rick-anderson
description: Core MVCASP.NET gelen isteklerin URL'lerini eşleştirmek ve bunları eylemlerle eşlemek için Yönlendirme Middleware'i nasıl kullandığını öğrenin.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 74afd0a076ca8bd753000f547ef0a26308e8a884
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123498"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core'daki eylemleri denetleyiciye yönlendirme

Ryan [Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core denetleyicileri gelen isteklerin URL'leri eşlemek ve [eylemlerle](#action)eşlemek için Yönlendirme [ara ware](xref:fundamentals/middleware/index) kullanın.  Rotaşablonları:

* Başlangıç kodu veya öznitelikleri tanımlanır.
* URL yollarının [eylemlerle](#action)nasıl eşleştiğini açıklayın.
* Bağlantılar için URL oluşturmak için kullanılır. Oluşturulan bağlantılar genellikle yanıtlar olarak döndürülür.

Eylemler geleneksel [olarak yönlendirilir](#cr) veya [öznitelik-yönlendirilir.](#ar) Denetleyiciye veya [eyleme](#action) bir rota yerleştirmek, rotayı öznitelik yönlendiren yapar. Daha fazla bilgi için [Karışık yönlendirmeye](#routing-mixed-ref-label) bakın.

Bu belge:

* MVC ve yönlendirme arasındaki etkileşimleri açıklar:
  * Tipik MVC uygulamaları yönlendirme özelliklerinden nasıl yararlanıyor?
  * Her ikisini de kapsar:
    * [Geleneksel yönlendirme](#cr) genellikle denetleyicileri ve görünümleri ile kullanılır.
    * REST API'leri ile kullanılan *öznitelik* yönlendirmesi. Öncelikle REST API'leri yönlendirmeyle ilgileniyorsanız, REST [API'leri için Öznitelik yönlendirmesine](#ar) atlayın.
  * Gelişmiş yönlendirme ayrıntıları için [Yönlendirme'ye](xref:fundamentals/routing) bakın.
* ASP.NET Core 3.0'a eklenen ve bitiş noktası yönlendirmesi olarak adlandırılan varsayılan yönlendirme sistemini ifade eder. Uyumluluk amacıyla yönlendirmenin önceki sürümüne sahip denetleyicileri kullanmak mümkündür. Talimatlar için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın. Eski yönlendirme sistemindereferans materyali için [bu belgenin 2.2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Geleneksel rotayı ayarlama

`Startup.Configure`genellikle [geleneksel yönlendirme](#crd)kullanırken aşağıdakilere benzer bir kodvardır:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Çağrının içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , tek bir rota oluşturmak için kullanılır. Tek rota rota `default` olarak adlandırılır. Denetleyicileri ve görünümleri olan uygulamaların çoğu, `default` rotaya benzer bir rota şablonu kullanır. REST API'ler [öznitelik yönlendirme](#ar)kullanmalıdır.

Rota şablonu: `"{controller=Home}/{action=Index}/{id?}"`

* Url yolunu eşleştirin`/Products/Details/5`
* Yolu belirterek `{ controller = Products, action = Details, id = 5 }` rota değerlerini ayıklar. Uygulamanın adında `ProductsController` bir denetleyicisi ve bir `Details` eylemi varsa, rota değerlerinin çıkarılması eşleşir:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`modeli `id = 5` `id` parametreyi ' ye ayarlamak için `5`değerini bağlar. Daha fazla ayrıntı için [Model Bağlama'ya](xref:mvc/models/model-binding) bakın.
* `{controller=Home}`varsayılan `controller` `Home` olarak tanımlar.
* `{action=Index}`varsayılan `action` `Index` olarak tanımlar.
*  Karakter `?` isteğe `id` bağlı olarak `{id?}` tanımlar.
  * Varsayılan ve isteğe bağlı rota parametrelerinin eşleşme için URL yolunda bulunması gerekmez. Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.
* URL yolu `/`yla eşleşir.
* Rota değerlerini `{ controller = Home, action = Index }`üretir.

Varsayılan değerler `controller` `action` için değerler ve kullanım. `id`URL yolunda karşılık gelen bir segment olmadığından bir değer üretmez. `/`yalnızca bir `HomeController` ve `Index` eylem varsa eşleşir:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Önceki denetleyici tanımı ve yol şablonu `HomeController.Index` kullanılarak, eylem aşağıdaki URL yolları için çalıştırılır:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL yolu, `/` rota şablonu varsayılan `Home` denetleyicilerini ve `Index` eylemi kullanır. URL yolu, `/Home` rota şablonu varsayılan `Index` eylemini kullanır.

Kolaylık yöntemi: <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>

```csharp
endpoints.MapDefaultControllerRoute();
```

Değiştirir:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> Yönlendirme, ara yazılım ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ara yazılım kullanılarak yapılandırılır. Denetleyicileri kullanmak için:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> [Yönlendirilmiş](#ar) denetleyicileri eşlemek için içini `UseEndpoints` arayın.
> * Geleneksel <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> [olarak yönlendirilmiş](#cr) denetleyicileri haritalamak için arayın veya.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Konvansiyonel yönlendirme

Geleneksel yönlendirme denetleyicileri ve görünümleri ile kullanılır. Rota: `default`

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

geleneksel bir *yönlendirme*örneğidir. URL yolları için bir *kural* oluşturmuştur çünkü *geleneksel yönlendirme* denir:

* İlk yol kesimi, `{controller=Home}`denetleyici adı eşler.
* İkinci bölüm, `{action=Index}` [eylem](#action) adı eşler.
* Üçüncü segment, `{id?}` isteğe bağlı `id`olarak kullanılır. In `?` `{id?}` isteğe bağlı hale getirir. `id`bir model varlık için eşlemek için kullanılır.

Bu `default` yolu kullanarak, URL yolu:

* `/Products/List`eylem eharitalar. `ProductsController.List`
* `/Blog/Article/17`haritaları `BlogController.Article` ve genellikle model parametreyi `id` 17'ye bağlar.

Bu haritalama:

* Denetleyici ve [eylem](#action) adlarını temel alan **sadece.**
* Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel almıyor.

Varsayılan rota ile geleneksel yönlendirme kullanarak her eylem için yeni bir URL deseni ile gelmek zorunda kalmadan uygulama oluşturma sağlar. KUMANDALAR arasında URL'ler için tutarlılık olan [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) tarzı eylemlere sahip bir uygulama için:

* Kodu basitleştirmeye yardımcı olur.
* UI'yi daha öngörülebilir hale getirir.

> [!WARNING]
> Önceki `id` kod, rota şablonu tarafından isteğe bağlı olarak tanımlanır. Eylemler, URL'nin bir parçası olarak sağlanan isteğe bağlı kimlik olmadan yürütülebilir. Genellikle,`id` URL'den atlandığında:
>
> * `id`model bağlama `0` tarafından ayarlanır.
> * Veritabanında eşleşen `id == 0`varlık bulunamadı.
>
> [Öznitelik yönlendirme,](#ar) kimliğin diğerleri için değil, bazı eylemler için gerekli olmasını sağlamak için ince taneli denetim sağlar. Sözleşmeye göre, belgeler doğru `id` kullanımda görünme olasılıkları gibi isteğe bağlı parametreleri içerir.

Çoğu uygulama, URL'lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel `{controller=Home}/{action=Index}/{id?}`rota:

* Temel ve açıklayıcı yönlendirme düzenini destekler.
* UI tabanlı uygulamalar için yararlı bir başlangıç noktasıdır.
* Birçok web Ara Birimi uygulaması için gereken tek rota şablonudur. Daha büyük web kullanıcı arabirimi uygulamaları için, sık sık gereken tüm alanlar [kullanarak](#areas) başka bir rota.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Çağrıldıkları sıraya göre uç noktalarına otomatik olarak bir **sipariş** değeri atayın.

Core 3.0 ve sonraki ASP.NET'da bitiş noktası yönlendirmesi:

* Güzergah kavramı yok.
* Genişletilebilirliğin yürütülmesi için sipariş garantisi sağlamaz, tüm uç noktalar aynı anda işlenir.

Yerleşik yönlendirme uygulamalarının <xref:Microsoft.AspNetCore.Routing.Route>istekleri nasıl eşleştirbildiğini görmek için [Günlüğe Kaydetme'yi](xref:fundamentals/logging/index) etkinleştirin.

[Öznitelik yönlendirme](#ar) süresonra bu belgede açıklanıyor.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Birden fazla konvansiyonel rota

Birden fazla [konvansiyonel rotalar](#cr) daha fazla <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>çağrı ekleyerek içine `UseEndpoints` eklenebilir ve . Bunu yapmak, birden çok kuralın tanımlanmasına veya belirli bir [eyleme](#action)ayrılmış geleneksel yolların eklenmesine olanak tanır ( örneğin:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Önceki `blog` koddaki rota özel bir **geleneksel rotadır.** Buna özel konvansiyonel rota denir, çünkü:

* Bu [geleneksel yönlendirme](#cr)kullanır.
* Belirli bir [eyleme](#action)adanmıştır.

Çünkü `controller` `action` ve rota şablonunda `"blog/{*article}"` parametre olarak görünmeyin:

* Yalnızca varsayılan değerlere `{ controller = "Blog", action = "Article" }`sahip olabilirler.
* Bu rota her zaman `BlogController.Article`eylem eşler.

`/Blog`, `/Blog/Article`ve `/Blog/{any-string}` blog rotasıyla eşleşen tek URL yollarıdır.

Önceki örnek:

* `blog`önce ekilen rota, `default` maçlar için rotadan daha yüksek bir önceliğe sahiptir.
* URL'nin bir parçası olarak bir makale adı olması nın tipik olduğu [Sümüklü böcek](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirmesi ve örneğidir.

> [!WARNING]
> Core 3.0 ve daha sonra ASP.NET, yönlendirme şunları yapmaz:
> * Rota adı verilen bir kavram *tanımlayın.* `UseRouting`ara yazılım boru hattına eşleşen rota ekler. Ara `UseRouting` yazılım, uygulamada tanımlanan uç nokta kümesine bakar ve isteğe bağlı olarak en iyi bitiş noktası eşleşmesini seçer.
> * Gibi <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> genişletilebilirlik yürütme emri hakkında garanti <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>ler sağlayın.
>
>Yönlendirme yle ilgili referans materyali için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Konvansiyonel yönlendirme düzeni

Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyici nin bir birleşimi ile eşleşir. Bu, geleneksel yolların çakıştığı servis taleplerini basitleştirmek için tasarlanmıştır.
<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>Çağrıldıkları sıraya <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>göre, ", ve <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> otomatik olarak uç noktalarına bir sipariş değeri atay. Daha önce görünen bir rotadaki eşleşmelerin daha yüksek bir önceliği vardır. Konvansiyonel yönlendirme isteðe baðlð±r. Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik oldukları için daha erken yerleştirilmelidir. Gibi `{*article}` tüm rota parametrelerini yakalamak ile [özel geleneksel rotalar](#dcr) çok [açgözlü](xref:fundamentals/routing#greedy)bir rota yapabilirsiniz, diğer rotalar ile eşleşen olması amaçlanan URL'ler eşleşen anlamına gelir. Açgözlü eşleşmeleri önlemek için daha sonra rota tablosuna açgözlü rotaları koyun.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Belirsiz eylemleri çözme

İki uç nokta yönlendirme ile eşleştiğinde, yönlendirme aşağıdakilerden birini yapmalıdır:

* En iyi adayı seç.
* Bir istisna at.

Örneğin:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Önceki denetleyici eşleşen iki eylemi tanımlar:

* URL yolu`/Products33/Edit/17`
* Veri `{ controller = Products33, action = Edit, id = 17 }`yolu .

Bu, MVC denetleyicileri için tipik bir desendir:

* `Edit(int)`bir ürünü sağlamak için bir form görüntüler.
* `Edit(int, Product)`deftere nakledilen formu işler.

Doğru rotayı çözmek için:

* `Edit(int, Product)`istek bir HTTP `POST`olduğunda seçilir.
* `Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir. `Edit(int)`genellikle üzerinden `GET`denir.

, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, yönlendirmeye sağlanır, böylece isteğin HTTP yöntemine göre seçim yapabilir. Daha `HttpPostAttribute` `Edit(int, Product)` iyi bir `Edit(int)`maç yapar.

Bu gibi `HttpPostAttribute`özniteliklerin rolünü anlamak önemlidir. Benzer öznitelikler diğer [HTTP fiilleri](#verb)için tanımlanır. [Geleneksel yönlendirmede,](#cr)eylemlerin gösteri formunun bir parçası olduklarında aynı eylem adını kullanması, form iş akışı göndermesi yaygındır. Örneğin, [bkz.](xref:tutorials/first-mvc-app/controller-methods-views#get-post)

Yönlendirme en iyi adayı seçemiyorsa, birden çok eşleşen uç noktayı listeleyen bir <xref:System.Reflection.AmbiguousMatchException> atılmıştır.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Geleneksel rota adları

Dizeleri `"blog"` ve `"default"` aşağıdaki örneklerde geleneksel rota adları şunlardır:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Rota adları rotaya mantıksal bir ad verir. Adlandırılmış rota URL üretimi için kullanılabilir. Adlandırılmış bir rotanın kullanılması, yolların sıralanması URL oluşturmayı karmaşık hale getirebileceğinde URL oluşturmayı kolaylaştırır. Rota adları benzersiz uygulama genişliğinde olmalıdır.

Rota adları:

* URL eşleştirme veya isteklerin işlenmesi üzerinde hiçbir etkisi yoktur.
* Yalnızca URL üretimi için kullanılır.

Rota adı kavramı [yönlendirmede IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)olarak temsil edilir. Terimrota **adı** ve bitiş **noktası adı:**

* Değiştirilebilir.
* Hangisinin belge ve kodda kullanıldığı açıklanan API'ye bağlıdır.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST API'leri için öznitelik yönlendirme

REST API'ler, uygulamanın işlevselliğini işlemlerin [HTTP fiilleri](#verb)tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır.

Öznitelik yönlendirme, eylemleri doğrudan rota şablonlarına yönlendirmek için bir öznitelik kümesi kullanır. Aşağıdaki `StartUp.Configure` kod REST API için tipiktir ve bir sonraki örnekte kullanılır:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> eşöz `UseEndpoints` yönlendirilmiş denetleyicileri eşlemek için içinde denir.

Aşağıdaki örnekte:

* Önceki `Configure` yöntem kullanılır.
* `HomeController`varsayılan geleneksel rotanın `{controller=Home}/{action=Index}/{id?}` eşleştiğininbenzer URL'leri kümesiyle eşleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Eylem, `HomeController.Index` `/`URL yollarından herhangi biri `/Home` `/Home/Index`için `/Home/Index/3`çalıştırılır, , , veya .

Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasındaki önemli bir programlama farkvurgulamaktadır. Öznitelik yönlendirme, bir rota belirtmek için daha fazla giriş gerektirir. Geleneksel varsayılan rota yolları daha kısa bir şekilde işler. Ancak, öznitelik yönlendirme, her [eylem](#action)için hangi rota şablonlarının geçerli olduğunu kesin olarak denetlemeye izin verir ve gerektirir.

Öznitelik yönlendirmesi ile denetleyici adı ve eylem adları eylemin eşleştir olduğu **hiçbir** rol oynamaz. Aşağıdaki örnek, önceki örnekle aynı URL'lerle eşleşir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Aşağıdaki kod için `action` belirteç `controller`replasmanı kullanır ve:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Aşağıdaki kod denetleyici `[Route("[controller]/[action]")]` için geçerlidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Önceki kodda, `Index` yöntem şablonları rota `/` şablonlarına hazırlanmalıdır. `~/` Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları.

Rota şablonu seçimiyle ilgili bilgi için [Rota şablonu önceliğine](xref:fundamentals/routing#rtp) bakın.

## <a name="reserved-routing-names"></a>Ayrılmış yönlendirme adları

Denetleyicileri veya Jilet Sayfaları kullanırken aşağıdaki anahtar kelimeler rota parametre adları ayrılmıştır:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Öznitelik yönlendirmesi ile bir rota parametresi olarak kullanmak `page` yaygın bir hatadır. Bunu yapmak, URL oluşturma yla tutarsız ve kafa karıştırıcı davranışlara neden olur.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Özel parametre adları, URL oluşturma işleminin Bir Jilet Sayfası'na mı yoksa Denetleyiciye mi atıfta bulunduğunu belirlemek için URL oluşturma tarafından kullanılır.

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP fiil şablonları

ASP.NET Core aşağıdaki HTTP fiil şablonları vardır:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[Posta]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Rota şablonları

ASP.NET Core aşağıdaki rota şablonlarına sahiptir:

* Tüm [HTTP fiil şablonları](#verb) rota şablonlarıdır.
* [[Rota]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Http fiil öznitelikleri ile öznitelik yönlendirme

Aşağıdaki denetleyiciyi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Yukarıdaki kodda:

* Her eylem, `[HttpGet]` yalnızca HTTP GET istekleriyle eşleşen öznitelik içerir.
* `GetProduct` Eylem şablonu `"{id}"` içerir, `id` bu nedenle `"api/[controller]"` denetleyicideki şablona eklenir. Yöntem şablonu. `"api/[controller]/"{id}""` Bu nedenle bu eylem sadece form `/api/test2/xyz``/api/test2/123`için`/api/test2/{any string}`GET isteklerieş , , , vb.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct` Eylem şablonu `"int/{id:int}")` içerir. Şablonun `:int` `id` bölümü, rota değerlerini tamsayıya dönüştürülebilecek dizeleri kısıtlar. Bir GET `/api/test2/int/abc`isteği :
  * Bu hareketle uyuşmuyor.
  * [404 Bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hata verir.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Eylem `GetInt2Product` şablonda içerir, `{id}` ancak bir karşıcıya dönüştürülebilecek değerlerle sınırlandırmaz. `id` Bir GET `/api/test2/int2/abc`isteği :
  * Bu rotayla eşleşiyor.
  * Model bağlama bir `abc` karşıcıya dönüştürmede başarısız olur. Yöntemin `id` parametresi, tümsecidir.
  * Model bağlama bir tamsayıya dönüştürülemediğinden`abc` [400 Kötü İstek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Öznitelik yönlendirme, , <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> ve <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. gibi öznitelikleri kullanabilirsiniz. [TÜM HTTP fiil](#verb) öznitelikleri bir rota şablonu kabul eder. Aşağıdaki örnek, aynı rota şablonuyla eşleşen iki eylemi gösterir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL yolunu `/products3`kullanma:

* `MyProductsController.ListProducts` EYLEM, HTTP [fiili](#verb) . `GET`
* `MyProductsController.CreateProduct` EYLEM, HTTP [fiili](#verb) . `POST`

BIR REST API'si inşa ederken, eylem tüm `[Route(...)]` HTTP yöntemlerini kabul ettiğinden, bir eylem yönteminde kullanmanız gereken nadirdir. API'nizin neyi desteklediği hakkında kesin olmak için daha spesifik [HTTP fiil özniteliğini](#verb) kullanmak daha iyidir. REST API'lerinin istemcilerinin belirli mantıksal işlemlere hangi yolların ve HTTP fiillerinin eşlenebildiğini bilmeleri beklenir.

REST API'leri, uygulamanın işlevselliğini işlemlerin HTTP fiilleri tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır. Bu, aynı mantıksal kaynaktaki GET ve POST gibi birçok operasyonun aynı URL'yi kullandığı anlamına gelir. Öznitelik yönlendirme, bir API'nin genel uç nokta düzenini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Bir öznitelik rotası belirli bir eylem için geçerli olduğundan, rota şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Eylem: `Products2ApiController.GetProduct(int)`

* GIBI URL yolu ile çalıştırılır mı`/products2/3`
* URL yolu `/products2`ile çalıştırılmıyor.

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlamak için bir eyleme izin verir. Daha fazla bilgi için [bkz.](xref:web-api/index#consumes)

 Rota şablonlarının ve ilgili seçeneklerin tam açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

Daha fazla `[ApiController]`bilgi için [ApiController özniteliğine](xref:web-api/index##apicontroller-attribute)bakın.

## <a name="route-name"></a>Yönlendirme adı

Aşağıdaki kod bir rota adı `Products_List`tanımlar:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Rota adları, belirli bir rotayı temel alan bir URL oluşturmak için kullanılabilir. Rota adları:

* Yönlendirmenin URL eşleştirme davranışı üzerinde hiçbir etkisi yoktur.
* Yalnızca URL üretimi için kullanılır.

Rota adları uygulama genelinde benzersiz olmalıdır.

Önceki kodu, parametreyi `id` isteğe bağlı olarak tanımlayan geleneksel`{id?}`varsayılan rotayla karşılayın ( ). API'leri tam olarak belirtme olanağının, `/products` farklı `/products/5` eylemlere izin verme ve gönderme gibi avantajları vardır.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Öznitelik yollarını birleştirme

Öznitelik yönlendirmeyi daha az yinelenen hale getirmek için denetleyicideki rota öznitelikleri, tek tek eylemlerdeki rota öznitelikleriyle birleştirilir. Denetleyicide tanımlanan tüm rota şablonları, eylemlerdeki şablonları yönlendirmeye hazırlanır. Denetleyiciye bir rota özniteliği yerleştirmek, denetleyicideki **tüm** eylemleri öznitelik yönlendirmesi kullanır.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Önceki örnekte:

* URL yolu `/products` eşleşebilir`ProductsApi.ListProducts`
* URL yolu `/products/5` eşleşebilir. `ProductsApi.GetProduct(int)`

Bu eylemlerin her ikisi `GET` de yalnızca HTTP `[HttpGet]` ile eşleşir, çünkü öznitelik ile işaretlenirler.

Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları. Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolu kümesiyle eşleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Aşağıdaki tabloda `[Route]` önceki koddaki öznitelikler açıklanmaktadır:

| Öznitelik               | Ile birleştirir`[Route("Home")]` | Rota şablonu tanımlar |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Evet | `"Home"` |
| `[Route("Index")]` | Evet | `"Home/Index"` |
| `[Route("/")]` | **Hayır** | `""` |
| `[Route("About")]` | Evet | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Öznitelik rota sırası

Yönlendirme bir ağaç oluşturur ve tüm uç noktaları eş zamanlı olarak eşleşir:

* Rota girişleri ideal bir sıralamaya yerleştirilmiş gibi olur.
* En özel rotalar, daha genel rotalardan önce yürütme şansına sahiptir.

Örneğin, gibi `blog/search/{topic}` bir öznitelik yolu gibi `blog/{*article}`bir öznitelik yolu daha özeldir. Rota, `blog/search/{topic}` varsayılan olarak daha yüksek önceliğe sahiptir, çünkü daha spesifiktir. [Geleneksel yönlendirmeyi](#cr)kullanarak, geliştirici yolları istenilen sıraya yerleştirmekten sorumludur.

Öznitelik yolları <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> özelliği kullanarak bir sipariş yapılandırabilirsiniz. Sağlanan tüm çerçeve rota `Order` [öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) içerir. Rotalar, artan bir `Order` özellik türüne göre işlenir. Varsayılan sıra. `0` Sipariş belirlemeyen `Order = -1` rotalardan önce çalıştırmaları kullanarak rota ayarlama. Varsayılan rota `Order = 1` siparişinden sonra çalışır kullanarak bir rota ayarlama.

Bağlı olarak `Order` **kaçının.** Bir uygulamanın URL alanı doğru yönlendirmek için açık sipariş değerleri gerektiriyorsa, istemciler için de kafa karıştırıcı olabilir. Genel olarak, öznitelik yönlendirme, URL eşlemeile doğru rotayı seçer. URL oluşturma için kullanılan varsayılan sipariş çalışmıyorsa, geçersiz kılma olarak bir rota adı `Order` kullanmak genellikle özelliği uygulamaktan daha kolaydır.

Her ikisi de rota eşleşen `/home`tanımlamak aşağıdaki iki denetleyicileri düşünün:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Önceki `/home` kodla birlikte istemek aşağıdakilere benzer bir özel durum oluşturur:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Rota `Order` özniteliklerinden birine ekleme belirsizliği giderir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Önceki kodla bitiş `/home` noktasını `HomeController.Index` çalıştırın. Almak için `MyDemoController.MyIndex`, `/home/MyIndex`istek . **Not**:

* Önceki kod bir örnek veya kötü yönlendirme tasarımıdır. `Order` Mülkü göstermek için kullanılmış.
* Özellik `Order` yalnızca belirsizliği giderir, bu şablon eşleşemez. Şablonu `[Route("Home")]` kaldırmak daha iyi olacaktır.

[Bkz. Razor Pages rota ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) Razor Pages ile rota sırası hakkında bilgi için rota sırası.

Bazı durumlarda, bir HTTP 500 hatası belirsiz yollarla döndürülür. Hangi uç noktaların neden `AmbiguousMatchException`olduğunu görmek için [günlüğe kaydetmeyi](xref:fundamentals/logging/index) kullanın.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Rota şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]

Kolaylık sağlamak için, öznitelik rotaları aşağıdakilerden birine bir belirteç ekleyerek ayrılmış rota parametreleri için belirteç değişimini destekler:

* Kare ayraçlar:`[]`
* Kıvırcık ayraçlar:`{}`

Yol `[action]` `[area]`tanımlandığı eylemden `[controller]` eylem adı, alan adı ve denetleyici adının değerleri ile değiştirilir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Yukarıdaki kodda:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Eşleşen`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Eşleşen`/Products0/Edit/{id}`

Belirteç değiştirme, öznitelik yollarını oluşturmanın son adımı olarak gerçekleşir. Önceki örnek aşağıdaki kodla aynı şekilde davranışta dır:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Öznitelik yolları da devralma ile birleştirilebilir. Bu belirteç değiştirme ile birlikte güçlüdür. Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`her eylem için benzersiz bir rota adı oluşturur:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
her eylem için benzersiz bir rota adı oluşturur.

Gerçek belirteç değiştirme delimiter `[` `]`maç veya , karakter (veya)`[[` `]]`tekrarlayarak kaçmak.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değiştirmesini özelleştirmek için parametre transformatörü kullanma

Belirteç değiştirme parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü parametrelerin değerini uygular <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini aşağıdakilere değiştirir:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

Bir <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> uygulama modeli kuralı:

* Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.
* Değiştirildikleri gibi öznitelik rotası belirteç değerlerini özelleştirin.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Önceki `ListAll` yöntem eşleşir. `/subscription-management/list-all`

Bir `RouteTokenTransformerConvention` seçenek olarak `ConfigureServices`kaydedilir.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Sümüklüböcek tanımı için [Slug'daki MDN web dokümanlarına](https://developer.mozilla.org/docs/Glossary/Slug) bakın.

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Birden çok öznitelik yolu

Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yol tanımlamayı destekler. Bunun en yaygın kullanımı, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel rotanın davranışını taklit etmektir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Denetleyiciye birden çok yol öznitelikleri koymak, her birinin eylem yöntemlerindeki her bir rota öznitelikleriyle birleştiği anlamına gelir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Tüm [HTTP fiil](#verb) yolu `IActionConstraint`kısıtlamaları uygular.

Uygulayan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> birden çok rota öznitelikleri bir eyleme yerleştirildiğinde:

* Her eylem kısıtlaması denetleyiciye uygulanan rota şablonuyla birleştirir.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Eylemler üzerinde birden fazla rota kullanmak yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmak daha iyidir. Varolan istemcileri desteklemek için **yalnızca** gerektiğinde eylemlerde birden çok yol kullanın.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Öznitelik rotası isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtme

Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır sıralı sözdizimini destekler.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Önceki kodda, `[HttpPost("product/{id:int}")]` bir rota kısıtlaması uygular. Eylem `ProductsController.ShowProduct` yalnızca `/product/3`URL yollarla eşleşir. Rota şablonu `{id:int}` bölümü, bu kesimi yalnızca tamsayılarla kısıtlar.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>IRouteTemplateProvider kullanarak özel rota öznitelikleri

Tüm [rota öznitelikleri](#rt) uygular. <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> ASP.NET Core çalışma zamanı:

* Uygulama başladığında denetleyici sınıfları ve eylem yöntemleriyle ilgili öznitelikleri arar.
* İlk rota kümesini oluşturmak için uygulanan `IRouteTemplateProvider` öznitelikleri kullanır.

Özel `IRouteTemplateProvider` rota özniteliklerini tanımlamak için uygulayın. Her `IRouteTemplateProvider` biri özel bir rota şablonu, sipariş ve ad ile tek bir rota tanımlamanızı sağlar:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Önceki `Get` yöntem döndürür. `Order = 2, Template = api/MyTestApi`

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Öznitelik rotalarını özelleştirmek için uygulama modelini kullanma

Uygulama modeli:

* Başlangıçta oluşturulan bir nesne modelidir.
* ASP.NET Core tarafından bir uygulamadaki eylemleri yönlendirmek ve yürütmek için kullanılan tüm meta verileri içerir.

Uygulama modeli, rota özniteliklerinden toplanan tüm verileri içerir. Rota özniteliklerinden gelen veriler `IRouteTemplateProvider` uygulama tarafından sağlanır. Kural -ları:

* Yönlendirmenin nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde gerçekleştirileceğini özelleştirmek için uygulama modelini değiştirmek için yazılabilir.
* Uygulama başlangıç noktasında okunur.

Bu bölümde, uygulama modelini kullanarak yönlendirme özelleştirme temel bir örnek gösterilmektedir. Aşağıdaki kod, yolların kabaca projenin klasör yapısıyla hizaya dizilmesini sağlar.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Aşağıdaki kod, öznitelik yönlendirilmiş denetleyicilere kuralın `namespace` uygulanmasını engeller:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Örneğin, aşağıdaki denetleyici aşağıdakileri `NamespaceRoutingConvention`kullanmaz:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Yöntem: `NamespaceRoutingConvention.Apply`

* Denetleyici öznitelik yönlendirilmişse hiçbir şey yapmaz.
* Denetleyicişablonu `namespace`, tabanı `namespace` kaldırılmış olarak ayarlar.

`Startup.ConfigureServices`Uygulanabilir: `NamespaceRoutingConvention`

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Yukarıdaki kodda:

* `My.Application`Üs. `namespace`
* Önceki denetleyicinin tam `My.Application.Admin.Controllers.UsersController`adı.
* Denetleyicişablonu `NamespaceRoutingConvention` `Admin/Controllers/Users/[action]/{id?`ayarlar.

Denetleyiciye `NamespaceRoutingConvention` öznitelik olarak da uygulanabilir:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Karışık yönlendirme: Atfetme yönlendirme vs konvansiyonel yönlendirme

ASP.NET Core uygulamaları geleneksel yönlendirme ve öznitelik yönlendirme kullanımını karıştırabilir. Tarayıcılar için HTML sayfaları sunan denetleyiciler için geleneksel rotalar kullanmak ve REST API'leri sunan denetleyiciler için yönlendirme özelliği kullanmak tipiktir.

Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir. Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar. Öznitelik yollarını tanımlayan eylemlere geleneksel yollardan ve bunun tersi üzerinden ulaşılamıyor. Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki **tüm** eylemleri yönlendirilen yapar.

Öznitelik yönlendirme ve konvansiyonel yönlendirme aynı yönlendirme motorkullanın.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL Oluşturma ve ortam değerleri

Uygulamalar, eylemlere URL bağlantıları oluşturmak için yönlendirme URL oluşturma özelliklerini kullanabilir. URL'ler üretmek, kodlama URL'lerini ortadan kaldırarak kodu daha sağlam ve korunabilir hale getirir. Bu bölümde, MVC tarafından sağlanan URL oluşturma özellikleri üzerinde duruluyor ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsamaktadır. URL oluşturmanın ayrıntılı bir açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

Arabirim, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> MVC ile URL üretimi için yönlendirme arasındaki altyapının temel öğesidir. Bir örneği `IUrlHelper` denetleyiciler, `Url` görünümler ve görünüm bileşenleri özelliği aracılığıyla kullanılabilir.

Aşağıdaki örnekte, `IUrlHelper` arabirim `Controller.Url` başka bir eylem için bir URL oluşturmak için özellik üzerinden kullanılır.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel rotayı kullanıyorsa, `url` değişkenin değeri URL `/UrlGeneration/Destination`yol dizesidir. Bu URL yolu, şu yolu birleştirerek yönlendirme yle oluşturulur:

* **Ortam değerleri**olarak adlandırılan geçerli istekten rota değerleri.
* Bu değerlere `Url.Action` geçirilen ve bu değerlerin yerine geçen değerler:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Rota şablonundaki her rota parametresinin değeri, adları değerler ve ortam değerleriyle eşleştirerek ikame edilmiştir. Değeri olmayan bir rota parametresi şunları yapabilir:

* Varsa varsayılan bir değer kullanın.
* İsteğe bağlıysa atlanmalıdır. Örneğin, rota `id` şablonundan. `{controller}/{action}/{id?}`

Gerekli rota parametresi karşılık gelen bir değere sahip değilse URL oluşturma başarısız olur. URL oluşturma bir rota için başarısız olursa, sonraki rota tüm yollar denenene veya eşleşme bulunana kadar denendi.

Önceki örnek, `Url.Action` geleneksel [yönlendirmeyi](#cr)varsayar. URL oluşturma, kavramlar farklı olsa da [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır. Konvansiyonel yönlendirme ile:

* Rota değerleri şablonu genişletmek için kullanılır.
* Bu şablonda `controller` `action` genellikle rota değerleri görüntülenir. Yönlendirme yle eşleşen URL'ler bir sözleşmeye uyduğu için bu işe yarar.

Aşağıdaki örneköznite yönlendirme kullanır:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Önceki `Source` koddaki eylem oluşturur. `custom/url/to/destination`

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>alternatif olarak Core 3.0ASP.NET eklendi. `IUrlHelper` `LinkGenerator`benzer ancak daha esnek işlevsellik sunar. Her yöntem `IUrlHelper` üzerinde `LinkGenerator` de ilgili bir yöntem ailesi vardır.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL oluşturma

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), ve ilgili tüm overloads tüm bir denetleyici adı ve eylem adı belirterek hedef bitiş noktası oluşturmak için tasarlanmıştır.

Kullanırken, `Url.Action`geçerli rota `controller` değerleri `action` için ve çalışma zamanı tarafından sağlanır:

* Değeri `controller` ve `action` hem ortam değerleri ve [değerlerinin](#ambient) bir parçasıdır. Yöntem `Url.Action` her zaman geçerli `action` değerleri `controller` kullanır ve geçerli eyleme giden bir URL yolu oluşturur.

Yönlendirme, URL oluştururken sağlanmadı bilgileri doldurmak için ortam değerlerindeki değerleri kullanmaya çalışır. Ortam değerleri `{ a = Alice, b = Bob, c = Carol, d = David }` `{a}/{b}/{c}/{d}` gibi bir rota düşünün:

* Yönlendirme, ek değerler olmadan bir URL oluşturmak için yeterli bilgiye sahiptir.
* Yönlendirme, tüm rota parametrelerinin bir değeri olduğundan yeterli bilgiye sahiptir.

Katma değer `{ d = Donovan }` eklenirse:

* Değer `{ d = David }` yoksayılır.
* Oluşturulan URL `Alice/Bob/Carol/Donovan`yolu.

**Uyarı**: URL yolları hiyerarşiktir. Önceki örnekte, değer `{ c = Cheryl }` eklenirse:

* Her iki `{ c = Carol, d = David }` değer de yoksayılır.
* Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.
* Bir URL `c` oluşturmak `d` için istenen değerler belirtilmelidir.  

Varsayılan rota `{controller}/{action}/{id?}`ile bu sorunu vurmak bekleyebilirsiniz. Her zaman a `Url.Action` `controller` ve `action` değeri açıkça belirttiğinden, bu sorun pratikte nadirdir.

[Url.Action'ın](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) birkaç aşırı yükü, rota parametreleri için `controller` değerler `action`sağlamak için bir rota değerleri nesnesi alır ve . Rota değerleri nesnesi sık `id`sık . Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`. Rota değerleri nesnesi:

* Kural olarak genellikle anonim türde bir nesnedir.
* Bir `IDictionary<>` veya [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).

Rota parametreleri ile eşleşmeyen ek rota değerleri sorgu dizesinde konur.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Önceki kod `/Products/Buy/17?color=red`oluşturur.

Aşağıdaki kod mutlak bir URL oluşturur:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Mutlak bir URL oluşturmak için aşağıdakilerden birini kullanın:

* Bir aşırı yük kabul `protocol`eder. Örneğin, önceki kod.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), varsayılan olarak mutlak URIs oluşturur.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Rotaya göre URL'ler oluşturma

Önceki kod denetleyici ve eylem adı geçerek bir URL oluşturma gösterdi. `IUrlHelper`ayrıca [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) yöntemleri ailesi sağlar. Bu yöntemler [Url.Action'a](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)benzer, ancak geçerli değerleri `action` ve `controller` rota değerlerini kopyalamaz. En yaygın `Url.RouteUrl`kullanımı:

* URL'yi oluşturmak için bir rota adı belirtir.
* Genellikle bir denetleyici veya eylem adı belirtmez.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Aşağıdaki Razor dosyası için bir HTML `Destination_Route`bağlantısı oluşturur:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>HTML ve Razor'da URL'ler oluşturma

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> [html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini `<form>` `<a>` sağlar ve sırasıyla öğeleri sağlar. Bu yöntemler url oluşturmak için [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler. Bunun `Url.RouteUrl` için `HtmlHelper` refakatçive `Html.BeginRouteForm` `Html.RouteLink` benzer işlevsellik vardır.

`form` TagHelpers, TagHelper ve `<a>` TagHelper aracılığıyla URL'ler oluşturur. Bunların her `IUrlHelper` ikisi de bunların uygulanması için kullanılır. Daha fazla bilgi için [Formlarda Yardımcıları Etiketle'](xref:mvc/views/working-with-forms) ye bakın.

İç görünümler, `IUrlHelper` yukarıda `Url` yer almayan herhangi bir özel URL nesli için özellik üzerinden kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Eylem Sonuçlarında URL oluşturma

Önceki örnekler bir `IUrlHelper` denetleyicide kullanılarak gösterilmektedir. Denetleyicide en yaygın kullanım, eylem sonucunun bir parçası olarak bir URL oluşturmaktır.

Ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuran eylem sonuçları için kolaylık yöntemleri sağlar. Tipik bir kullanım kullanıcı girişi kabul ettikten sonra yeniden yönlendirmek için:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Eylem gibi fabrika yöntemleri <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> sonuçları ve üzerinde `IUrlHelper`yöntemlere benzer bir desen izleyin.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Özel konvansiyonel rotalar için özel durum

[Konvansiyonel yönlendirme,](#cr) özel bir [konvansiyonel rota](#dcr)adı verilen özel bir rota tanımı türü kullanabilir. Aşağıdaki örnekte, adlandırılmış `blog` rota özel bir geleneksel rotadır:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Önceki rota tanımlarını kullanarak, `Url.Action("Index", "Home")` `/` `default` yolu kullanarak URL yolunu oluşturur, ancak neden? Rota değerlerinin `{ controller = Home, action = Index }` bir URL oluşturmak `blog`için yeterli olacağını tahmin edebilirsiniz ve sonuç . `/blog?action=Index&controller=Home`

[Özel geleneksel rotalar,](#dcr) rotanın URL oluşturma yla çok [açgözlü](xref:fundamentals/routing#greedy) olmasını engelleyen karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır. Bu durumda varsayılan değerler `{ controller = Blog, action = Article }`, `controller` ve `action` ne ne de bir rota parametresi olarak görünür. Yönlendirme URL oluşturma gerçekleştirirken, sağlanan değerler varsayılan değerlerle eşleşmelidir. Değerler `blog` `{ controller = Home, action = Index }` eşleşmediği `{ controller = Blog, action = Article }`için URL oluşturma başarısız olur. Yönlendirme sonra denemek `default`için geri düşer , hangi başarılı.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Alanlar,](xref:mvc/controllers/areas) ilgili işlevleri ayrı olarak bir gruba düzenlemek için kullanılan bir MVC özelliğidir:

* Denetleyici eylemler için ad alanını yönlendirme.
* Görünümler için klasör yapısı.

Alanları kullanmak, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyiciye sahip olmasını sağlar. Alanları kullanmak, `area` başka bir rota parametresi ekleyerek yönlendirme amacıyla bir `controller` `action`hiyerarşi oluşturur. Bu bölümde yönlendirmenin alanlarla nasıl etkileştiği açıklanmıştır. Görünümlerle alanların nasıl kullanıldığıyla ilgili ayrıntılar için [Alanlar'a](xref:mvc/controllers/areas) bakın.

Aşağıdaki örnekte, Varsayılan geleneksel rotayı ve `area` adlandırılmış `Blog` `area` bir rotayı kullanacak Şekilde MVC yapılandırılır:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> oluşturmak için `"blog_route"`çağrılır. İkinci parametre, `"Blog"`alan adıdır.

Bir URL yolu `/Manage/Users/AddUser`gibi `"blog_route"` eşleştirirken, rota `{ area = Blog, controller = Users, action = AddUser }`rota değerlerini oluşturur. Rota `area` değeri için varsayılan değer `area`tarafından üretilir. Oluşturulan `MapAreaControllerRoute` rota aşağıdakilere eşdeğerdir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`bu durumda, `Blog`sağlanan alan adını kullanmak `area` için hem varsayılan değeri hem de kısıtlamayı kullanarak bir rota oluşturur. Varsayılan değer, rotanın her `{ area = Blog, ... }`zaman üretir, kısıtlama `{ area = Blog, ... }` URL oluşturma için değer gerektirir sağlar.

Konvansiyonel yönlendirme isteðe baðlð±r. Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik oldukları için daha erken yerleştirilmelidir.

Önceki örneği kullanarak, rota `{ area = Blog, controller = Users, action = AddUser }` değerleri aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[Alan]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir denetleyiciyi bir alanın parçası olarak gösterir. Bu denetleyici `Blog` bölgede. Özniteliği olmayan `[Area]` denetleyiciler herhangi bir alanın üyesi değildir ve `area` rota değeri yönlendirme ile sağlandığında **eşleşmez.** Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota `{ area = Blog, controller = Users, action = AddUser }`değerleriyle eşleşebilir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Her denetleyicinin ad alanı tamolmak için burada gösterilir. Önceki denetleyiciler aynı ad alanını kullanırsa, derleyici hatası oluşturulur. Sınıf ad alanlarının MVC'nin yönlendirmesi üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adları `area` rota değeri tarafından sağlandığında eşleşir. Üçüncü denetleyici herhangi bir alanın üyesi değildir ve yalnızca yönlendirme `area` ile değer sağlanmadığı zaman eşleşebilir.

<a name="aa"></a>

Hiçbir *değer*eşleştirme açısından, `area` değer yokluğu için `area` değer null veya boş dize sanki aynıdır.

Bir alan içinde bir eylem yürütürken, rota değeri `area` URL oluşturma için kullanılacak yönlendirme için ortam [değeri](#ambient) olarak kullanılabilir. Bu, varsayılan olarak alanların URL oluşturma için aşağıdaki örnekte gösterildiği gibi *yapışkan* davrandığı anlamına gelir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Aşağıdaki kod için bir `/Zebra/Users/AddUser`URL oluşturur:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Eylem tanımı

[Eylem Dışı](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özelliği olanlar hariç, denetleyicideki ortak yöntemler eylemlerdir.

## <a name="sample-code"></a>Örnek kod

 * [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) yöntemi [örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ye dahildir ve yönlendirme bilgilerini görüntülemek için kullanılır.
* [Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC, gelen isteklerin URL'lerini eşleştirmek ve bunları eylemlerle eşleştirmek için Yönlendirme [aracını](xref:fundamentals/middleware/index) kullanır. Yollar başlangıç kodunda veya özniteliklerinde tanımlanır. Rotalar, URL yollarının eylemlerle nasıl eşleşeceğini açıklar. Rotalar, yanıtlar olarak gönderilen URL'ler (bağlantılar için) oluşturmak için de kullanılır.

Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir. Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar. Daha fazla bilgi için [Karışık yönlendirmeye](#routing-mixed-ref-label) bakın.

Bu belge, MVC ve yönlendirme arasındaki etkileşimleri ve tipik MVC uygulamalarının yönlendirme özelliklerinden nasıl yararlandığını açıklar. Gelişmiş yönlendirme yle ilgili ayrıntılar için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

## <a name="setting-up-routing-middleware"></a>Yönlendirme Middleware'ini ayarlama

*Yapılandırma* yönteminizde şuna benzer bir kod görebilirsiniz:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Çağrının `UseMvc`içinde, `MapRoute` `default` rota olarak adlandırılacağız tek bir rota oluşturmak için kullanılır. Çoğu MVC `default` uygulaması, rotaya benzer bir şablona sahip bir rota kullanır.

Rota şablonu `"{controller=Home}/{action=Index}/{id?}"` gibi `/Products/Details/5` bir URL yolu eşleşebilir ve yolu belirterek rota değerlerini `{ controller = Products, action = Details, id = 5 }` ayıklar. MVC adlı `ProductsController` bir denetleyici bulmak ve `Details`eylem çalıştırmak için çalışacağız:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Bu örnekte, model bağlama bu eylemi `id = 5` çağırırken `id` parametre `5` ayarlamak için değerini kullanacağını unutmayın. Daha fazla ayrıntı için [Model Bağlama'ya](../models/model-binding.md) bakın.

Rotayı `default` kullanma:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Rota şablonu:

* `{controller=Home}`varsayılan `Home` olarak tanımlar`controller`

* `{action=Index}`varsayılan `Index` olarak tanımlar`action`

* `{id?}`isteğe `id` bağlı olarak tanımlar

Varsayılan ve isteğe bağlı rota parametrelerinin eşleşme için URL yolunda bulunması gerekmez. Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.

`"{controller=Home}/{action=Index}/{id?}"`URL yolu `/` eşleşebilir ve rota `{ controller = Home, action = Index }`değerleri üretecek. URL yolunda `controller` `action` karşılık gelen bir segment `id` olmadığından, varsayılan değerlerin değerleri ve varsayılan değerleri kullanma değerleri bir değer üretmez. MVC, eylemi `HomeController` ve eylemi `Index` seçmek için bu rota değerlerini kullanır:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Bu denetleyici tanımı ve yol `HomeController.Index` şablonu kullanılarak, eylem aşağıdaki URL yollarından herhangi biri için yürütülür:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Kolaylık yöntemi: `UseMvcWithDefaultRoute`

```csharp
app.UseMvcWithDefaultRoute();
```

Değiştirmek için kullanılabilir:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`ve `UseMvcWithDefaultRoute` ara yazılım `RouterMiddleware` boru hattına bir örnek ekleyin. MVC ara yazılımlarla doğrudan etkileşime girmez ve istekleri işlemek için yönlendirme kullanır. MVC bir örneği ile yollara `MvcRouteHandler`bağlanır. İçindeki `UseMvc` kod aşağıdakilere benzer:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`doğrudan herhangi bir rota tanımlamaz, `attribute` rota için rota koleksiyonuna bir yer tutucu ekler. Aşırı yük, `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.  `UseMvc`ve tüm varyasyonları öznitelik yolu için bir yer tutucu ekleyin - öznitelik yönlendirme nasıl `UseMvc`yapılandırırsanız yapılandırırsanız yapının her zaman kullanılabilir. `UseMvcWithDefaultRoute`varsayılan bir rota tanımlar ve öznitelik yönlendirmeyi destekler. [Öznitelik Yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Konvansiyonel yönlendirme

Rota: `default`

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Önceki kod, geleneksel bir yönlendirme örneğidir. URL yolları için bir *kural kuralı* oluşturduğundan, bu stil geleneksel yönlendirme olarak adlandırılır:

* İlk yol kesimi denetleyici adı eşler.
* Eylem adının ikinci eşleri.
* Üçüncü segment isteğe bağlı `id`olarak kullanılır. `id`bir model varlık için eşler.

`default` Bu yolu kullanarak, `/Products/List` URL yolu `ProductsController.List` eyleme `/Blog/Article/17` eşler ve `BlogController.Article`eşler. Bu eşleme **yalnızca** denetleyici ve eylem adlarını temel alınarak ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel almamaktadır.

> [!TIP]
> Varsayılan rota ile geleneksel yönlendirme yi kullanmak, tanımladığınız her eylem için yeni bir URL deseni bulmak zorunda kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır. CRUD tarzı eylemleri olan bir uygulama için, denetleyicilerinizdeki URL'ler için tutarlılık olması kodunuzu basitleştirmeye ve kullanıcı aranızı daha öngörülebilir hale getirmeye yardımcı olabilir.

> [!WARNING]
> Rota `id` şablonu tarafından isteğe bağlı olarak tanımlanır, bu da eylemlerinizin URL'nin bir parçası olarak sağlanan kimlik olmadan yürütülebileceği anlamına gelir. URL'den atlanırsa genellikle ne olur, `id` model bağlama `0` tarafından ayarlanacak ve sonuç olarak veritabanı eşleşen `id == 0`hiçbir varlık bulunamaz. Öznitelik yönlendirme, bazı eylemler için gerekli olan kimliği yapmak için, diğerleri için değil, ince taneli denetim sağlayabilir. Sözleşmeye göre belgeler, doğru `id` kullanımda görünme olasılıkları gibi isteğe bağlı parametreleri içerir.

## <a name="multiple-routes"></a>Birden çok rota

Daha fazla çağrı `UseMvc` ekleyerek içine birden `MapRoute`çok yol ekleyebilirsiniz. Bunu yapmak, birden çok kural tanımlamanıza veya belirli bir eyleme adanmış geleneksel rotalar eklemenize olanak tanır:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Buradaki `blog` rota özel bir *konvansiyonel rotadır,* yani geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme adanmıştır. `controller` Rota `action` şablonunda parametre olarak görünmediğinden ve görünmediğinden, yalnızca varsayılan değerlere sahip olabilirler `BlogController.Article`ve bu nedenle bu yol her zaman eylemle eşlenecektir.

Rota koleksiyonundaki rotalar sipariş edilir ve eklendikleri sırada işlenir. Bu örnekte, `blog` rota rotadan `default` önce denenecektir.

> [!NOTE]
> *Özel geleneksel rotalar* genellikle URL `{*article}` yolunun kalan kısmını yakalamak gibi **tüm yolları yakalama** parametrelerini kullanır. Bu, diğer rotalarla eşleşmek istediğiniz URL'lerle eşleştiğini anlamına gelen bir rotayı 'çok açgözlü' yapabilir. Bunu çözmek için daha sonra rota tablosuna 'açgözlü' yolları koyun.

### <a name="fallback"></a>Geri dönüş

İstek işlemenin bir parçası olarak, MVC rota değerlerinin uygulamanızda bir denetleyici ve eylem bulmak için kullanılabileceğini doğrular. Rota değerleri bir eylemle eşleşmiyorsa, rota eşleşmez ve bir sonraki rota denenecektir. Buna *geri dönüş*denir ve geleneksel yolların çakıştığı durumlarda basitleştirilmesi amaçlanır.

### <a name="disambiguating-actions"></a>Disambiguating eylemleri

İki eylem yönlendirme yoluyla eşleştiğinde, MVC 'en iyi' adayı seçmek için ayrıştırmalı veya başka bir özel durum atmalıdır. Örneğin:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Bu denetleyici, URL yolu `/Products/Edit/17` ve rota verileri `{ controller = Products, action = Edit, id = 17 }`eşleşecek iki eylem tanımlar. Bu, bir ürünü düzenlemek için `Edit(int)` bir form gösterdiği ve `Edit(int, Product)` deftere nakledilen formu işlediği MVC denetleyicileri için tipik bir desendir. Bunu mümkün kılmak için MVC `Edit(int, Product)` istek bir HTTP `POST` `Edit(int)` ve ne zaman HTTP fiil başka bir şey olduğunu seçmek gerekir.

`HttpPostAttribute` ( `[HttpPost]` ) ( ) `IActionConstraint` yalnızca http fiili . `POST` Bir `IActionConstraint` varlığı `Edit(int, Product)` daha bir 'daha iyi' maç yapar `Edit(int)`, bu yüzden `Edit(int, Product)` ilk denenecektir.

Yalnızca özel senaryolarda `IActionConstraint` özel uygulamalar yazmanız gerekir, ancak benzer `HttpPostAttribute` öznitelikler diğer HTTP fiilleri için tanımlanır gibi özniteliklerin rolünü anlamak önemlidir. Geleneksel yönlendirmede, eylemlerin `show form -> submit form` iş akışının bir parçası olduklarında aynı eylem adını kullanması yaygındır. Bu desenin kolaylığı, [Anlama IActionConstraint](#understanding-iactionconstraint) bölümünü inceledikten sonra daha belirgin hale gelecektir.

Birden çok rota eşleşirse ve MVC 'en iyi' `AmbiguousActionException`rota bulamazsa, bir .

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Rota adları

Dizeleri `"blog"` ve `"default"` aşağıdaki örneklerde rota adları şunlardır:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Rota adları, adlandırılmış rotanın URL üretimi için kullanılabileceğini görebilmek için rotaya mantıksal bir ad verir. Bu, yolların sıralanması URL oluşturmayı karmaşık hale getirebildiği zaman URL oluşturmayı büyük ölçüde kolaylaştırır. Rota adları uygulama genelinde benzersiz olmalıdır.

Rota adlarının URL eşleştirmesi veya isteklerin işlenmesi üzerinde hiçbir etkisi yoktur; yalnızca URL üretimi için kullanılırlar. [Yönlendirme,](xref:fundamentals/routing) MVC'ye özel yardımcılarda URL üretimi de dahil olmak üzere URL oluşturma hakkında daha ayrıntılı bilgilere sahiptir.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Öznitelik yönlendirme

Öznitelik yönlendirme, eylemleri doğrudan rota şablonlarına yönlendirmek için bir öznitelik kümesi kullanır. Aşağıdaki örnekte, `app.UseMvc();` `Configure` yöntem kullanılır ve hiçbir rota geçirilir. Varsayılan `HomeController` rotanın `{controller=Home}/{action=Index}/{id?}` eşleşeceği URL'lerle eşleşecek:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Eylem, `HomeController.Index()` URL `/`yollarından herhangi biri `/Home`için `/Home/Index`yürütülecektir, veya .

> [!NOTE]
> Bu örnek, öznitelik yönlendirme ve geleneksel yönlendirme arasındaki önemli bir programlama farkVurgulamaktadır. Öznitelik yönlendirme, bir rota belirtmek için daha fazla giriş gerektirir; geleneksel varsayılan rota yolları daha kısa bir şekilde işler. Ancak, öznitelik yönlendirme, her eylem için hangi rota şablonlarının geçerli olduğunu niçin hassas bir şekilde denetlemeye izin verir (ve gerektirir).

Öznitelik yönlendirme ile denetleyici adı ve eylem adları hangi eylem seçilir **hiçbir** rol oynar. Bu örnek, önceki örnekle aynı URL'lerle eşleşir.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Yukarıdaki rota şablonları `action`, ve `area`. `controller` Aslında, bu rota parametreleri öznitelik yollarında izin verilmez. Rota şablonu zaten bir eylemle ilişkili olduğundan, eylem adını URL'den ayrıştırmak mantıklı olmaz.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http[Fiil] öznitelikleri ile öznitelik yönlendirme

Öznitelik yönlendirme `Http[Verb]` si gibi `HttpPostAttribute`öznitelikleri de yararlanabilir. Bu özniteliklerin tümü bir rota şablonu kabul edebilir. Bu örnek, aynı rota şablonu eşleşen iki eylem gösterir:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Http fiili olduğunda `/products` `ProductsApi.ListProducts` `GET` eylem gibi bir URL yolu `ProductsApi.CreateProduct` yürütülür ve HTTP `POST`fiili . Öznitelik yönlendirmesi ilk olarak URL ile rota öznitelikleri tarafından tanımlanan rota şablonları kümesiyle eşleşir. Bir rota şablonu `IActionConstraint` eşleştikten sonra, hangi eylemlerin yürütülebileceğini belirlemek için kısıtlamalar uygulanır.

> [!TIP]
> Bir REST API'si inşa ederken, eylem `[Route(...)]` tüm HTTP yöntemlerini kabul edeceği için bir eylem yönteminde kullanmak isteyeceğiniz nadirdir. API'nizin neyi desteklediği `Http*Verb*Attributes` konusunda kesin olmak için daha spesifik olanı kullanmak daha iyidir. REST API'lerinin istemcilerinin belirli mantıksal işlemlere hangi yolların ve HTTP fiillerinin eşlenebildiğini bilmeleri beklenir.

Bir öznitelik rotası belirli bir eylem için geçerli olduğundan, rota şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Eylem `ProductsApi.GetProduct(int)` gibi `/products/3` bir URL yolu için yürütülür, `/products`ancak gibi bir URL yolu için değil. Rota şablonlarının ve ilgili seçeneklerin tam açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

## <a name="route-name"></a>Rota Adı

Aşağıdaki kod bir *rota* adı `Products_List`tanımlar:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Rota adları, belirli bir rotayı temel alan bir URL oluşturmak için kullanılabilir. Rota adlarının yönlendirmenin URL eşleştirme davranışı üzerinde hiçbir etkisi yoktur ve yalnızca URL oluşturma için kullanılır. Rota adları uygulama genelinde benzersiz olmalıdır.

> [!NOTE]
> Bunu, parametreyi `id` isteğe bağlı olarak tanımlayan geleneksel`{id?}` *varsayılan rotayla*karşılaştırın ( ). API'leri tam olarak belirtme becerisinin, `/products` farklı `/products/5` eylemlere izin verme ve gönderilme gibi avantajları vardır.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Yolları birleştirme

Öznitelik yönlendirmeyi daha az yinelenen hale getirmek için denetleyicideki rota öznitelikleri, tek tek eylemlerdeki rota öznitelikleriyle birleştirilir. Denetleyicide tanımlanan tüm rota şablonları, eylemlerdeki şablonları yönlendirmeye hazırlanır. Denetleyiciye bir rota özniteliği yerleştirmek, denetleyicideki **tüm** eylemleri öznitelik yönlendirmesi kullanır.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

Bu örnekte URL `/products` yolu `ProductsApi.ListProducts`eşleşebilir `/products/5` ve `ProductsApi.GetProduct(int)`URL yolu eşleşebilir. Bu eylemlerin her ikisi `GET` de sadece HTTP `HttpGetAttribute`eşleşir, çünkü onlar ile işaretlenirler.

Denetleyiciye uygulanan rota şablonlarıyla `/` başlayan `~/` veya birleşmeyen bir eyleme uygulanan rota şablonları. Bu örnek, *varsayılan rotaya*benzer bir URL yolu kümesiyle eşleşir.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Öznitelik rotalarını sıralama

Tanımlı bir sırada yürüten geleneksel yolların aksine, öznitelik yönlendirmesi bir ağaç oluşturur ve tüm rotalarla eşleşir. Bu, rota girişleri ideal bir sıralamaya yerleştirilmişse, bu şekilde olur; en özel yolları daha genel yolları önce yürütmek için bir şans var.

Örneğin, gibi `blog/search/{topic}` bir rota, .' `blog/{*article}`gibi bir rotadan daha belirgindir. Mantıksal olarak `blog/search/{topic}` rota 'çalışır' ilk, varsayılan olarak, çünkü tek mantıklı sipariş konuşuyor. Geleneksel yönlendirmeyi kullanarak, geliştirici yolları istenilen sıraya yerleştirmekten sorumludur.

Öznitelik yolları, sağlanan tüm çerçevenin `Order` özelliğini kullanarak bir siparişi yapılandırabilir. Rotalar, artan bir `Order` özellik türüne göre işlenir. Varsayılan sıra. `0` Bir rotayı `Order = -1` kullanarak ayarlama, sipariş belirlemeyen rotalardan önce çalışır. Varsayılan rota `Order = 1` siparişinden sonra bir rota yı kullanmak çalışır.

> [!TIP]
> Bağlı olarak `Order`kaçının. URL alanınız doğru yönlendirmek için açık sipariş değerleri gerektiriyorsa, istemciler için de kafa karıştırıcı olabilir. Genel öznitelik yönlendirme, URL eşlemi ile doğru rotayı seçer. URL oluşturma için kullanılan varsayılan sipariş çalışmıyorsa, rota adını geçersiz kılma olarak kullanmak `Order` genellikle özelliği uygulamaktan daha kolaydır.

Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. Razor Pages konularında rota sırası ile ilgili bilgiler [Razor Pages rota ve uygulama kurallarında mevcuttur: Rota sırası.](xref:razor-pages/razor-pages-conventions#route-order)

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Rota şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])

Kolaylık sağlamak için, öznitelik rotaları, bir belirteç imal`[` `]`eden bir belirteç imal ederek *belirteç değiştirmeyi* destekler ( , ). `[action]`Belirteçler, `[area]`ve `[controller]` rota tanımlandığı eylemeylem eylem eylem adı, alan adı ve denetleyici adı değerleri ile değiştirilir. Aşağıdaki örnekte, eylemler yorumlarda açıklandığı gibi URL yollarla eşleşir:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Belirteç değiştirme, öznitelik yollarını oluşturmanın son adımı olarak gerçekleşir. Yukarıdaki örnek aşağıdaki kodla aynı şekilde olacaktır:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Öznitelik yolları da devralma ile birleştirilebilir. Bu özellikle belirteç değiştirme ile birlikte güçlüdür.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Belirteç değiştirme, öznitelik yolları tarafından tanımlanan rota adları için de geçerlidir. `[Route("[controller]/[action]", Name="[controller]_[action]")]`her eylem için benzersiz bir rota adı oluşturur.

Gerçek belirteç değiştirme delimiter `[` `]`maç veya , karakter (veya)`[[` `]]`tekrarlayarak kaçmak.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değiştirmesini özelleştirmek için parametre transformatörü kullanma

Belirteç değiştirme parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü parametrelerin değerini uygular `IOutboundParameterTransformer` ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre `SubscriptionManagement` transformatörü `subscription-management`rota değerini .

Bir `RouteTokenTransformerConvention` uygulama modeli kuralı:

* Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.
* Değiştirildikleri gibi öznitelik rotası belirteç değerlerini özelleştirin.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

Bir `RouteTokenTransformerConvention` seçenek olarak `ConfigureServices`kaydedilir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Birden Fazla Rota

Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yol tanımlamayı destekler. Bunun en yaygın kullanımı, aşağıdaki örnekte gösterildiği gibi *varsayılan geleneksel rotanın* davranışını taklit etmektir:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Denetleyiciye birden çok yol öznitelikleri koymak, her birinin eylem yöntemlerindeki her bir rota öznitelikleriyle birleşeceği anlamına gelir.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Birden çok yol öznitelikleri (bu uygulama) `IActionConstraint`bir eylem ekikonulduğunda, her eylem kısıtlaması onu tanımlayan öznitelikten rota şablonuyla birleşir.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Eylemler üzerinde birden çok yol kullanmak güçlü görünse de, uygulamanızın URL alanını basit ve iyi tanımlanmış tutmak daha iyidir. Örneğin, varolan istemcileri desteklemek için yalnızca gerektiğinde eylemlerde birden çok yol kullanın.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Öznitelik rotası isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtme

Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır sıralı sözdizimini destekler.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Rota şablonu sözdiziminin ayrıntılı bir açıklaması için [Rota Şablonu Başvurusu'na](xref:fundamentals/routing#route-template-reference) bakın.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Özel rota öznitelikleri kullanarak`IRouteTemplateProvider`

Çerçevede sağlanan tüm rota öznitelikleri (, `[Route(...)]`, `[HttpGet(...)]` `IRouteTemplateProvider` vb.) arabirimi uygular. MVC, uygulama başlatıldığında denetleyici sınıfları ve eylem yöntemleriyle `IRouteTemplateProvider` ilgili öznitelikleri arar ve ilk rota kümesini oluşturmak için uygulayanları kullanır.

Kendi rota `IRouteTemplateProvider` özniteliklerinizi tanımlamak için uygulayabilirsiniz. Her `IRouteTemplateProvider` biri özel bir rota şablonu, sipariş ve ad ile tek bir rota tanımlamanızı sağlar:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Yukarıdaki örnekteki öznitelik otomatik olarak `Template` `"api/[controller]"` ne `[MyApiController]` zaman uygulanacağı ayarlar.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Öznitelik yollarını özelleştirmek için Uygulama Modelini kullanma

*Uygulama modeli,* mvc tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta verilerle başlangıçta oluşturulan bir nesne modelidir. *Uygulama modeli,* rota özniteliklerinden (aracılığıyla) `IRouteTemplateProvider`toplanan tüm verileri içerir. Yönlendirmenin nasıl bir şekilde nasıl bir şekilde nasıl bir şekilde nasıl olduğunu özelleştirmek için başlangıç zamanında uygulama modelini değiştirmek için *kurallar* yazabilirsiniz. Bu bölümde, uygulama modelini kullanarak yönlendirme özelleştirme basit bir örnek gösterilmektedir.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Karışık yönlendirme: Atfetme yönlendirme vs konvansiyonel yönlendirme

MVC uygulamaları geleneksel yönlendirme ve öznitelik yönlendirme kullanımını karıştırabilir. Tarayıcılar için HTML sayfaları sunan denetleyiciler için geleneksel rotalar kullanmak ve REST API'leri sunan denetleyiciler için yönlendirme özelliği kullanmak tipiktir.

Eylemler geleneksel olarak yönlendirilir veya öznitelik yönlendirilir. Denetleyiciye bir rota veya eylem yerleştirmek, rotayı bağlamasını sağlar. Öznitelik yollarını tanımlayan eylemlere geleneksel yollardan ve bunun tersi üzerinden ulaşılamıyor. Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemleri yönlendirilen yapar.

> [!NOTE]
> İki yönlendirme sistemi türünü diğerlerinden ayıran şey, bir URL rota şablonuyla eşleştikten sonra uygulanan işlemdir. Geleneksel yönlendirmede, eşleşmedeki rota değerleri, tüm geleneksel yönlendirilmiş eylemlerin bir arama tablosundan eylem ve denetleyiciyi seçmek için kullanılır. Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilidir ve başka bir arama gerekmez.

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]`), açgözlü olmayan bir şekilde sağdan sola literals eşleştirerek işlenir. Açıklamanın [kaynak koduna](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) bakın. Daha fazla bilgi için [bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL Oluşturma

MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir. URL'ler üretmek, kodlama URL'lerini ortadan kaldırarak kodunuzu daha sağlam ve korunabilir hale getirir. Bu bölüm, MVC tarafından sağlanan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar. URL oluşturmanın ayrıntılı bir açıklaması için [Yönlendirme'ye](xref:fundamentals/routing) bakın.

Arabirim, `IUrlHelper` MVC ve URL üretimi için yönlendirme arasındaki altyapının altında yatan parçadır. Denetleyiciler, görünümler `IUrlHelper` ve görünüm `Url` bileşenleri nde özellik aracılığıyla kullanılabilir bir örnek bulacaksınız.

Bu örnekte, `IUrlHelper` arabirim `Controller.Url` başka bir eylem için bir URL oluşturmak için özellik üzerinden kullanılır.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel rotayı kullanıyorsa, `url` değişkenin değeri URL `/UrlGeneration/Destination`yol dizesi olacaktır. Bu URL yolu, geçerli istekteki (ortam değerleri) rota değerlerini, geçirilen değerlerle birleştirerek `Url.Action` ve bu değerleri rota şablonuna ikame ederek yönlendirilerek oluşturulur:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Rota şablonundaki her rota parametresinin değeri, adları değerler ve ortam değerleriyle eşleştirerek ikame edilmiştir. Değeri olmayan bir rota parametresi varsa varsayılan bir değer kullanabilir veya isteğe bağlıysa atlanabilir (bu `id` örnekte olduğu gibi). Gerekli rota parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur. URL oluşturma bir rota için başarısız olursa, sonraki rota tüm yollar denenene veya eşleşme bulunana kadar denendi.

`Url.Action` Yukarıdaki örnek, geleneksel yönlendirme yi varsayar, ancak URL oluşturma kavramları farklı olsa da öznitelik yönlendirmeile benzer şekilde çalışır. Geleneksel yönlendirme ile, rota değerleri bir şablonu genişletmek için kullanılır `controller` `action` ve rota değerleri için ve genellikle bu şablonda görünür - yönlendirme ile eşleşen URL'ler bir *kural kuralına*uygun olduğu için çalışır. Öznitelik yönlendirmesinde, rota değerlerinin `controller` `action` şablonda görünmesine izin verilmez ve bunun yerine hangi şablonun kullanılacağını aramak için kullanılır.

Bu örnek, öznitelik yönlendirme kullanır:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC, yönlendirilen tüm eylemlerin bir arama tablosu oluşturur `controller` `action` ve URL oluşturma için kullanılacak rota şablonu seçmek için ve değerleri eşleşir. Yukarıdaki örnekte, `custom/url/to/destination` oluşturulur.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL oluşturma

`Url.Action` (`IUrlHelper` . `Action`) ve ilgili tüm aşırı yüklemelerin tümü, bir denetleyici adı ve eylem adı belirterek neye bağlandığınızı belirtmek istediğiniz fikrine dayanır.

> [!NOTE]
> Kullanırken, `Url.Action`geçerli rota `controller` değerleri `action` sizin için belirtilir ve `controller` `action` sizin için belirtilir - değeri ve hem *ortam değerleri* **ve** *değerlerinin*bir parçasıdır. Yöntem, `Url.Action`her zaman geçerli `action` değerleri `controller` kullanır ve geçerli eyleme yönlendirir bir URL yolu oluşturur.

Yönlendirme, URL oluştururken sağlamadığınız bilgileri doldurmak için ortam değerlerindeki değerleri kullanmaya çalışır. Rota gibi `{a}/{b}/{c}/{d}` bir rota ve `{ a = Alice, b = Bob, c = Carol, d = David }`ortam değerleri kullanarak, yönlendirme, tüm rota parametrelerinin bir değeri olduğundan, ek değer içermeyen bir URL oluşturmak için yeterli bilgiye sahiptir. Değeri `{ d = Donovan }`eklediyseniz, değer `{ d = David }` yoksayılır ve oluşturulan URL yolu `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> URL yolları hiyerarşiktir. Yukarıdaki örnekte, değeri `{ c = Cheryl }`eklediyseniz, her iki `{ c = Carol, d = David }` değer de yoksayılır. Bu durumda artık bir değerimiz `d` kalmaz ve URL oluşturma başarısız olur. İstenilen değeri belirtmeniz `c` `d`gerekir ve .  Bu sorunu varsayılan rotayla vurmayı`{controller}/{action}/{id?}`bekleyebilirsiniz ( ) - ancak her `Url.Action` zaman açıkça bir `controller` `action` ve değer belirteceği gibi uygulamada bu davranışla nadiren karşılaşırsınız.

Daha uzun `Url.Action` aşırı yükler, rota parametreleri dışındaki değerler sağlamak `controller` `action`için ek bir *rota değerleri* nesnesi de alır. En sık bu gibi `id` `Url.Action("Buy", "Products", new { id = 17 })`kullanılır göreceksiniz . Kural değeri nesnesi kural *değerlerine* göre genellikle anonim bir `IDictionary<>` nesnedir, ancak aynı zamanda eski bir *.NET nesnesi*de olabilir. Rota parametreleri ile eşleşmeyen ek rota değerleri sorgu dizesinde konur.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Mutlak bir URL oluşturmak için, bir: `protocol``Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Rotaya göre URL oluşturma

Yukarıdaki kod, denetleyici ve eylem adını geçerek bir URL oluşturmayı gösterdi. `IUrlHelper`aynı zamanda `Url.RouteUrl` yöntem ailesi sağlar. Bu yöntemler `Url.Action`, ancak geçerli değerleri `action` ve `controller` rota değerlerine kopyalamak yok benzer. En yaygın kullanım, genellikle bir denetleyici veya eylem adı *belirtmeden* URL'yi oluşturmak için belirli bir rotayı kullanmak üzere bir rota adı belirtmektir.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML'de URL oluşturma

`IHtmlHelper``HtmlHelper` yöntemleri `Html.BeginForm` sağlar `Html.ActionLink` ve `<form>` oluşturmak `<a>` ve elemanları sırasıyla. Bu yöntemler `Url.Action` bir URL oluşturmak için yöntemi kullanır ve benzer bağımsız değişkenleri kabul ederler. Bunun `Url.RouteUrl` için `HtmlHelper` refakatçive `Html.BeginRouteForm` `Html.RouteLink` benzer işlevsellik vardır.

`form` TagHelpers, TagHelper ve `<a>` TagHelper aracılığıyla URL'ler oluşturur. Bunların her `IUrlHelper` ikisi de bunların uygulanması için kullanılır. Daha fazla bilgi için [Formlarla Çalışma'ya](../views/working-with-forms.md) bakın.

İç görünümler, `IUrlHelper` yukarıda `Url` yer almayan herhangi bir özel URL nesli için özellik üzerinden kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Eylem Sonuçlarında URL Oluşturma

Yukarıdaki örnekler denetleyicide `IUrlHelper` kullanmayı gösterirken, denetleyicideki en yaygın kullanım eylem sonucunun bir parçası olarak bir URL oluşturmaktır.

Ve `ControllerBase` `Controller` temel sınıflar, başka bir eyleme başvuran eylem sonuçları için kolaylık yöntemleri sağlar. Tipik bir kullanım, kullanıcı girdisini kabul ettikten sonra yeniden yönlendirmektir.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Eylem sonuçları fabrika yöntemleri üzerinde `IUrlHelper`yöntemlere benzer bir desen izleyin.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Özel konvansiyonel rotalar için özel durum

Konvansiyonel yönlendirme, özel bir *konvansiyonel rota*adı verilen özel bir rota tanımı türü kullanabilir. Aşağıdaki örnekte, adlı `blog` rota özel bir geleneksel rotadır.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Bu rota tanımlarını `Url.Action("Index", "Home")` kullanarak, `default` rota `/` ile URL yolu oluşturur, ama neden? Rota değerlerinin `{ controller = Home, action = Index }` bir URL oluşturmak `blog`için yeterli olacağını tahmin edebilirsiniz ve sonuç . `/blog?action=Index&controller=Home`

Özel geleneksel rotalar, rotanın URL oluşturmayla "çok açgözlü" olmasını engelleyen karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır. Bu durumda varsayılan değerler `{ controller = Blog, action = Article }`, `controller` ve `action` ne ne de bir rota parametresi olarak görünür. Yönlendirme URL oluşturma gerçekleştirirken, sağlanan değerler varsayılan değerlerle eşleşmelidir. Değerler `blog` `{ controller = Home, action = Index }` eşleşmediği `{ controller = Blog, action = Article }`için URL oluşturma kullanımı başarısız olur. Yönlendirme sonra denemek `default`için geri düşer , hangi başarılı.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Alanlar,](areas.md) ilgili işlevselliği ayrı bir yönlendirme ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir gruba düzenlemek için kullanılan bir MVC özelliğidir. Alanları kullanmak, farklı *alanlara*sahip oldukları sürece, bir uygulamanın aynı ada sahip birden çok denetleyiciye sahip olmasını sağlar. Alanları kullanmak, `area` başka bir rota parametresi ekleyerek yönlendirme amacıyla bir `controller` `action`hiyerarşi oluşturur. Bu bölümde yönlendirmenin alanlarla nasıl etkileşimde olduğu tartışılır - alanların görünümlerle nasıl kullanıldığıyla ilgili ayrıntılar için [Alanlar'a](areas.md) bakın.

Aşağıdaki örnekte, Varsayılan geleneksel rotayı ve adlı bir alan `Blog`için alan *rotasını* kullanacak Şekilde MVC yapılandırılır:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Bir URL yolu `/Manage/Users/AddUser`gibi eşleştirirken, ilk rota `{ area = Blog, controller = Users, action = AddUser }`rota değerlerini üretecektir. `area` Rota değeri için `area`varsayılan bir değer tarafından üretilen , aslında tarafından `MapAreaRoute` oluşturulan rota aşağıdakilere eşdeğerdir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`bu durumda, `Blog`sağlanan alan adını kullanmak `area` için hem varsayılan değeri hem de kısıtlamayı kullanarak bir rota oluşturur. Varsayılan değer, rotanın her `{ area = Blog, ... }`zaman üretir, kısıtlama `{ area = Blog, ... }` URL oluşturma için değer gerektirir sağlar.

> [!TIP]
> Konvansiyonel yönlendirme isteðe baðlð±r. Genel olarak, alanları olan rotalar, alanı olmayan rotalardan daha spesifik olduğundan, rota tablosuna daha erken yerleştirilmelidir.

Yukarıdaki örneği kullanarak, rota değerleri aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Bir `AreaAttribute` denetleyiciyi bir alanın parçası olarak ifade eden şey, bu `Blog` denetleyicinin bölgede olduğunu söylüyoruz. Özniteliği olmayan `[Area]` denetleyiciler herhangi bir alanın üyesi değildir ve `area` rota değeri yönlendirme ile sağlandığında **eşleşmez.** Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota `{ area = Blog, controller = Users, action = AddUser }`değerleriyle eşleşebilir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Her denetleyicinin ad alanı burada tamolarak gösterilir - aksi takdirde denetleyicilerin bir adlandırma çakışması olur ve derleyici hatası oluşturur. Sınıf ad alanlarının MVC'nin yönlendirmesi üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adları `area` rota değeri tarafından sağlandığında eşleşir. Üçüncü denetleyici herhangi bir alanın üyesi değildir ve yalnızca yönlendirme `area` ile değer sağlanmadığı zaman eşleşebilir.

> [!NOTE]
> Hiçbir *değer*eşleştirme açısından, `area` değer yokluğu için `area` değer null veya boş dize sanki aynıdır.

Bir alan içinde bir eylem yürütürken, rota değeri `area` URL oluşturma için kullanılacak yönlendirme için bir ortam *değeri* olarak kullanılabilir. Bu, varsayılan olarak alanların URL oluşturma için aşağıdaki örnekte gösterildiği gibi *yapışkan* davrandığı anlamına gelir.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint'i Anlama

> [!NOTE]
> Bu bölüm, çerçeve içi ve MVC'nin yürütmek için nasıl bir eylem seçtiği konusunda derin bir dalıştır. Tipik bir uygulamanın özel bir`IActionConstraint`

Arabirimi bilmiyorsanız `IActionConstraint` bile büyük olasılıkla zaten kullanmışsınızdır. Öznitelik `[HttpGet]` ve `[Http-VERB]` benzeri `IActionConstraint` öznitelikleri bir eylem yönteminin yürütülmesini sınırlamak için uygular.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Varsayılan geleneksel rotayı varsayarsak, URL `/Products/Edit` `{ controller = Products, action = Edit }`yolu burada gösterilen eylemlerin **her ikisiyle de** eşleşen değerleri üretir. Terminolojide, `IActionConstraint` bu eylemlerin her ikisinin de rota verileriyle eşleşeceği için aday olarak kabul edilir.

`HttpGetAttribute` Yürütüldüğünde, *Edit()* *GET* için bir maç olduğunu ve başka bir HTTP fiiliyle eşleşmediğini söyler. Eylem `Edit(...)` tanımlanan herhangi bir kısıtlama yok ve bu yüzden herhangi bir HTTP fiil eşleşecektir. Yani varsayarak - `POST` sadece `Edit(...)` maçlar. Ancak, her `GET` iki eylem için de hala eşleşebilir - ancak, bir `IActionConstraint` eylem her zaman olmadan bir eylem daha *iyi* olarak kabul edilir. Bu `Edit()` nedenle `[HttpGet]` daha spesifik olarak kabul edilir ve her iki eylem eşleşebilir eğer seçilecektir.

Kavramsal olarak, `IActionConstraint` aşırı *yükleme*biçimidir, ancak aynı ada sahip aşırı yükleme yöntemleri yerine, aynı URL ile eşleşen eylemler arasında aşırı yükleme dir. Öznitelik yönlendirme de `IActionConstraint` kullanır ve farklı denetleyicileri her ikisi de aday olarak kabul edilen eylemleri neden olabilir.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint'in Uygulanması

Bir uygulamayı `IActionConstraint` uygulamanın en basit yolu, `System.Attribute` türetilen bir sınıf oluşturmak ve bunu eylemlerinizin ve denetleyicilerinizin üzerine yerleştirmektir. MVC, öznitelik `IActionConstraint` olarak uygulanan ları otomatik olarak keşfeder. Kısıtlamaları uygulamak için uygulama modelini kullanabilirsiniz ve bu, bunların nasıl uygulandığını meta programlamanıza olanak sağladığından büyük olasılıkla en esnek yaklaşımdır.

Aşağıdaki örnekte, bir kısıtlama rota verilerinden bir *ülke koduna* dayalı bir eylem seçer. [GitHub tam örnek](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Yöntemi uygulamaktan ve `Accept` kısıtlamanın yürütülmesi için bir 'Sipariş' seçmekten siz sorumlusunuz. Bu durumda, `Accept` eylemi `true` belirtmek için yöntem döner `country` rota değeri eşleştiğinde bir eşleşmedir. Bu, atfedilmeyen bir eyleme geri dönüşe izin verdiği açısından farklıdır. `RouteValueAttribute` Örnek, bir `en-US` eylem tanımlarsanız, benzer `fr-FR` bir ülke kodunun uygulanmamış `[CountrySpecific(...)]` daha genel bir denetleyiciye geri döneceğini gösterir.

Özellik, `Order` kısıtlamanın hangi *aşamanın* parçası olduğuna karar verir. Eylem kısıtlamaları gruplar halinde `Order`çalışır. Örneğin, http yöntemi öznitelikleri sağlanan çerçevenin `Order` tümü aynı aşamada çalıştırmak için aynı değeri kullanın. İstediğiniz ilkeleri uygulamak için gereken kadar aşamanız olabilir.

> [!TIP]
> Kısıtlamanızın HTTP yöntemlerine önce uygulanıp uygulanmayacağı hakkında düşünmek için `Order` bir değer eve karar vermek. Önce daha düşük sayılar çalışır.

::: moniker-end
