---
title: ASP.NET Core denetleyici eylemlerine yönlendirme
author: rick-anderson
description: ASP.NET Core MVC 'nin, gelen isteklerin URL 'Lerini eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme ara yazılımını nasıl kullandığını öğrenin.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 974a5e7653f2b71b124a96650733ff460e60637a
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206118"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core denetleyici eylemlerine yönlendirme

[Ryan şimdi ak](https://github.com/rynowak), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core denetleyicileri, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları [eylemlerle](#action)eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.  Rota şablonları:

* , Başlangıç kodunda veya özniteliklerde tanımlanmıştır.
* URL yollarının [eylemlerle](#action)nasıl eşleştiğini betimleyen.
* Bağlantıların URL 'Leri oluşturmak için kullanılır. Oluşturulan bağlantılar genellikle yanıtlar halinde döndürülür.

Eylemler [genel olarak-yönlendirildi](#cr) veya [Attribute-yönlendirildi](#ar)' dir. Bir yolun denetleyiciye veya [eyleme](#action) yerleştirilmesi, BT özniteliğinin yönlendirilmesini sağlar. Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .

Bu belge:

* MVC ve yönlendirme arasındaki etkileşimleri açıklar:
  * Tipik MVC uygulamalarının yönlendirme özelliklerini kullanma şekli.
  * Her ikisini de içerir:
    * Genellikle denetleyiciler ve görünümlerle kullanılan [genel olarak yönlendirme](#cr) .
    * REST API 'Leri ile kullanılan *öznitelik yönlendirme* . Birincil olarak REST API 'Leri için yönlendirme ile ilgileniyorsanız, [REST API 'leri Için öznitelik yönlendirme](#ar) bölümüne atlayın.
  * Bkz. Gelişmiş yönlendirme ayrıntıları için [yönlendirme](xref:fundamentals/routing) .
* ASP.NET Core 3,0 ' de eklenen varsayılan yönlendirme sisteminin Endpoint Routing olarak adlandırıldığını gösterir. Uyumluluk amaçlarıyla önceki yönlendirme sürümü ile denetleyicileri kullanmak mümkündür. Yönergeler için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın. Eski yönlendirme sistemindeki başvuru malzemeleri için [Bu belgenin 2,2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Geleneksel rotayı ayarlama

`Startup.Configure`genellikle [geleneksel yönlendirme](#crd)kullanılırken aşağıdakine benzer bir kod içerir:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Çağrısı içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> tek bir yol oluşturmak için kullanılır. Tek yol yol olarak adlandırılır `default` . Denetleyiciler ve görünümler içeren çoğu uygulama, `default` yola benzer bir rota şablonu kullanır. REST API 'Leri [öznitelik yönlendirmeyi](#ar)kullanmalıdır.

Yol şablonu `"{controller=Home}/{action=Index}/{id?}"`:

* Şöyle bir URL yoluyla eşleşir`/Products/Details/5`
* Yolu simgeleştirileyerek `{ controller = Products, action = Details, id = 5 }` yol değerlerini ayıklar. Uygulamanın adlı `ProductsController` bir denetleyici ve bir `Details` eylem varsa yol değerlerinin ayıklanması bir eşleşme ile sonuçlanır:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model, `id` parametresini olarak `5`ayarlamak `id = 5` için değerini bağlar. Daha fazla ayrıntı için bkz. [model bağlama](xref:mvc/models/model-binding) .
* `{controller=Home}`Varsayılan `Home` `controller`olarak tanımlar.
* `{action=Index}`Varsayılan `Index` `action`olarak tanımlar.
*  İçindeki `?` `{id?}` karakter, isteğe `id` bağlı olarak tanımlar.
  * Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez. Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .
* URL yoluyla `/`eşleşir.
* Yol değerlerini `{ controller = Home, action = Index }`üretir.

İçin `controller` değerleri ve `action` varsayılan değerleri kullanır. `id`URL yolunda karşılık gelen bir kesim olmadığından değer üretmez. `/`yalnızca bir `HomeController` ve `Index` eylemi varsa eşleşir:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Önceki denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem aşağıdaki URL yolları için çalıştırılır:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL yolu `/` , yönlendirme şablonu varsayılan `Home` denetleyicilerini ve `Index` eylemini kullanır. URL yolu `/Home` , yönlendirme şablonu varsayılan `Index` eylemini kullanır.

Kolaylık yöntemi <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Yerine

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Yönlendirme, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ara yazılımı kullanılarak yapılandırılır. Denetleyicileri kullanmak için:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> [Öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için içinde `UseEndpoints` çağırın.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> [Genel olarak yönlendirmeli](#cr) denetleyicileri eşlemek için veya <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>çağrısı yapın.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Geleneksel yönlendirme

Geleneksel yönlendirme, denetleyiciler ve görünümlerle kullanılır. `default` Yol:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

, *geleneksel yönlendirmeye*bir örnektir. Bu, URL yolları için bir *kural* oluşturduğundan *geleneksel yönlendirme* olarak adlandırılır:

* İlk yol segmenti, `{controller=Home}`, denetleyici adıyla eşlenir.
* İkinci kesim, `{action=Index}`, [eylem](#action) adıyla eşlenir.
* Üçüncü segment, isteğe `{id?}` bağlı `id`olarak kullanılır. İçinde `?` `{id?}` , isteğe bağlı yapar. `id`bir model varlığına eşlemek için kullanılır.

Bu `default` yolu kullanarak, URL yolu:

* `/Products/List``ProductsController.List` eyleme eşlenir.
* `/Blog/Article/17``BlogController.Article` ile eşlenir ve model genellikle `id` parametresini 17 ' ye bağlar.

Bu eşleme:

* **Yalnızca**denetleyiciyi ve [eylem](#action) adlarını temel alır.
* Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel alan değildir.

Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, her eylem için yeni bir URL düzeniyle karşılaşmadan uygulamanın oluşturulmasına olanak sağlar. [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) stilinde eylemlere sahip bir uygulama için, denetleyiciler arasında URL 'ler için tutarlılık vardır:

* Kodu basitleştirmeye yardımcı olur.
* Kullanıcı arabirimini daha öngörülebilir hale getirir.

> [!WARNING]
> Önceki `id` kodda, yol şablonu tarafından isteğe bağlı olarak tanımlanmıştır. Eylemler, URL 'nin bir parçası olarak belirtilen isteğe bağlı KIMLIK olmadan çalıştırılabilir. Genellikle,`id` URL 'den atlandığında:
>
> * `id`, model bağlama `0` tarafından olarak ayarlanır.
> * Veritabanında eşleşen `id == 0`bir varlık bulunamadı.
>
> [Öznitelik yönlendirme](#ar) , kimliği bazı eylemler için gerekli hale getirmek için ayrıntılı denetim sağlar ve diğerleri için değildir. Kurala göre belgeler, doğru kullanımlarda görünebilecekleri `id` gibi isteğe bağlı parametreler içerir.

Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}`:

* Temel ve açıklayıcı bir yönlendirme düzenini destekler.
* , UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.
* Birçok Web UI uygulaması için tek yol şablonu gereklidir. Daha büyük Web Kullanıcı arabirimi uygulamaları için, sık sık gerekli olan [alanlarda](#areas) başka bir yol kullanın.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* , Çağrıldığı sıraya göre kendi uç noktalarına otomatik olarak bir **sipariş** değeri atayın.

ASP.NET Core 3,0 ve üzeri için uç nokta yönlendirme:

* Bir yol kavramı yoktur.
* , Genişletilebilirlik yürütülmesi için sıralama garantisi sağlamaz, tüm uç noktalar bir kerede işlenir.

İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının <xref:Microsoft.AspNetCore.Routing.Route>nasıl yapıldığını görmek Için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin.

[Öznitelik yönlendirme](#ar) bu belgenin ilerleyen kısımlarında açıklanmıştır.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Birden çok geleneksel yollar

Ve <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ' <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>ye daha fazla çağrı eklenerek `UseEndpoints` , içine birden çok [geleneksel yol](#cr) eklenebilir. Bunun yapılması, birden çok kural tanımlamayı veya belirli bir [eyleme](#action)adanmış geleneksel yollar eklemeyi sağlar; örneğin:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Önceki `blog` koddaki yol, **ayrılmış bir geleneksel yoldur**. Ayrılmış bir geleneksel yol olarak adlandırılır çünkü:

* [Geleneksel yönlendirme](#cr)kullanır.
* Belirli bir [eyleme](#action)ayrılmıştır.

`controller` Ve `action` yol şablonunda `"blog/{*article}"` parametre olarak görünmediği için:

* Yalnızca varsayılan değerleri `{ controller = "Blog", action = "Article" }`olabilir.
* Bu yol her zaman eyleme `BlogController.Article`eşlenir.

`/Blog`, `/Blog/Article`ve `/Blog/{any-string}` , blog ROTASı ile eşleşen tek URL yollarıdır.

Önceki örnek:

* `blog`yol, önce eklendiğinden, `default` rotadan eşleşme için daha yüksek önceliğe sahiptir.
* Ve, URL 'nin bir parçası olarak bir makale adı olması gereken tipik bir [başlık](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirme örneğidir.

> [!WARNING]
> ASP.NET Core 3,0 ve üzeri sürümlerde yönlendirme:
> * *Yol*adlı bir kavram tanımlayın. `UseRouting`ara yazılım ardışık düzenine eşleşen rota ekler. `UseRouting` Ara yazılım, uygulamada tanımlanan uç noktalar kümesine bakar ve isteğe bağlı olarak en iyi uç nokta eşleşmesini seçer.
> * Veya <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>gibi genişletilebilirlik yürütme sırası hakkında garanti sağlar.
>
>Bkz. yönlendirme ile ilgili başvuru malzemeleri için [yönlendirme](xref:fundamentals/routing) .

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Geleneksel yönlendirme sırası

Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyicinin bir bileşimiyle eşleşir. Bu, geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.
, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, Ve <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> kullanarak yollar ekleme, çağırdıkları sıraya göre bitiş noktalarına otomatik olarak bir sipariş değeri atar. Daha önce görüntülenen bir rotadaki eşleşmelerin önceliği daha yüksektir. Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir. Tüm yol parametrelerini yakala gibi `{*article}` [ayrılmış geleneksel yollar](#dcr) , bir [yolu çok fazla](xref:fundamentals/routing#greedy), diğer yollarla eşleştirmek üzere hedeflediğiniz URL 'lerle eşleştirerek çok fazla bir yol yapabilir. Doyumsuz yollarını daha sonra yol tablosuna yerleştirerek doyumsuz eşleşmelerini önleyin.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Belirsiz eylemleri çözme

Yönlendirme ile iki uç nokta eşleşmesi durumunda, yönlendirme aşağıdakilerden birini yapmanız gerekir:

* En iyi aday ' ı seçin.
* Bir özel durum oluşturur.

Örneğin:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Yukarıdaki denetleyici, eşleşen iki eylemi tanımlar:

* URL yolu`/Products33/Edit/17`
* Veri `{ controller = Products33, action = Edit, id = 17 }`yönlendirin.

Bu, MVC denetleyicileri için tipik bir modeldir:

* `Edit(int)`bir ürünü düzenlemek için bir form görüntüler.
* `Edit(int, Product)`Postalanan formu işler.

Doğru yolu çözümlemek için:

* `Edit(int, Product)`istek bir HTTP `POST`olduğunda seçilir.
* `Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir. `Edit(int)`genellikle aracılığıyla `GET`çağrılır.

<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>,, İsteğin HTTP yöntemine göre seçim yapabilmesi için yönlendirme için `[HttpPost]`verilmiştir. , `HttpPostAttribute` Daha `Edit(int, Product)` iyi bir eşleşme yapar `Edit(int)`.

Gibi `HttpPostAttribute`özniteliklerin rolünü anlamanız önemlidir. Benzer öznitelikler diğer [http fiilleri](#verb)için tanımlanmıştır. [Geleneksel yönlendirmesinde](#cr), bir görüntüleme formu, gönder formu iş akışının bir parçası olduklarında aynı eylem adını kullanmak yaygın olarak kullanılan bir işlemdir. Örneğin, bkz. [Iki düzenleme eylemi yöntemini İnceleme](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Yönlendirme bir en iyi aday seçebilirse, birden <xref:System.Reflection.AmbiguousMatchException> fazla eşleşen uç noktayı listeleyerek bir oluşturulur.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Geleneksel yol adları

Dizeler `"blog"` ve `"default"` aşağıdaki örneklerde geleneksel yol adları verilmiştir:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Yol adları, yola mantıksal bir ad verir. Adlandırılmış yol, URL oluşturmak için kullanılabilir. Adlandırılmış bir yol kullanmak, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı basitleştirir. Yol adları benzersiz uygulama genelinde olmalıdır.

Yol adları:

* İsteklerin URL 'siyle eşleşmesi veya işlenmesi üzerinde hiçbir etkisi yoktur.
* Yalnızca URL oluşturmak için kullanılır.

Yol adı kavramı [ıendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)olarak yönlendirme ile temsil edilir. Terimler **yol adı** ve **uç nokta adı**:

* Değiştirilebilir.
* Belgede kullanılan ve kod, açıklanan API 'ye bağlıdır.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST API 'Leri için öznitelik yönlendirme

REST API 'Leri, uygulamanın işlevselliğini [http fiilleri](#verb)tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır.

Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır. Aşağıdaki `StartUp.Configure` kod, bir REST API için tipik bir sonraki örnekte kullanılır:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> öznitelik yönlendirmeli denetleyicileri eşlemek için `UseEndpoints` içinde çağırılır.

Aşağıdaki örnekte:

* Önceki `Configure` yöntem kullanılır.
* `HomeController`Varsayılan geleneksel yolun `{controller=Home}/{action=Index}/{id?}` eşleşdiküyle benzer bir URL kümesiyle eşleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index` Eylem `/`,, veya `/Home` `/Home/Index` `/Home/Index/3`URL yollarından herhangi biri için çalıştırılır.

Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasında bir temel programlama farkı vurgulamaktadır. Öznitelik yönlendirme için bir yol belirtmek için daha fazla giriş gerekir. Geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler. Ancak, öznitelik yönlendirme izin verir ve her [eyleme](#action)hangi rota şablonlarının uygulanacağını kesin olarak denetler.

Öznitelik yönlendirme ile, denetleyici adı ve eylem adları, eylem ile eşleşen **hiçbir** rol oynar. Aşağıdaki örnek, önceki örnekle aynı URL 'Lerle eşleşir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Aşağıdaki kod, ve `action` `controller`için belirteç yerini kullanır:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Aşağıdaki kod denetleyici için `[Route("[controller]/[action]")]` geçerlidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Yukarıdaki kodda, `Index` Yöntem şablonlarının sonuna `/` veya `~/` yol şablonlarına eklenmiş olması gerekir. Bir eyleme uygulanan `/` veya `~/` Bu denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.

Rota şablonu seçimi hakkında bilgi için bkz. [route Template önceliği](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Ayrılmış yönlendirme adları

Aşağıdaki anahtar sözcükler, denetleyiciler veya Razor Pages kullanılırken ayrılmış yol parametresi adlarıdır:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Öznitelik `page` yönlendirme ile yol parametresi olarak kullanmak yaygın bir hatadır. Bunun yapılması, URL oluşturma ile tutarsız ve kafa karıştırıcı davranışa neden olur.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Özel parametre adları, URL oluşturma işleminin bir Razor sayfasına mı yoksa bir denetleyiciye mi başvurduğunu öğrenmek için URL oluşturma tarafından kullanılır.

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP fiili şablonları

ASP.NET Core aşağıdaki HTTP fiili şablonlarına sahiptir:

* [HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Rota şablonları

ASP.NET Core aşağıdaki yol şablonlarına sahiptir:

* Tüm [http fiili şablonları](#verb) rota şablonlarıdır.
* [Yolu](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Http fiili öznitelikleriyle öznitelik yönlendirme

Aşağıdaki denetleyiciyi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Yukarıdaki kodda:

* Her eylem yalnızca HTTP `[HttpGet]` Get istekleriyle eşleştirmeyi kısıtlayan özniteliği içerir.
* `GetProduct` Eylem `"{id}"` , şablonu içerir, bu nedenle `id` denetleyicideki `"api/[controller]"` şablonun sonuna eklenir. Yöntemler şablonu `"api/[controller]/"{id}""`. Bu nedenle bu eylem yalnızca `/api/test2/xyz`,`/api/test2/123``/api/test2/{any string}`, vb. için get istekleri ile eşleşir.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct` Eylem `"int/{id:int}")` şablonu içerir. Şablonun `:int` kısmı, `id` yol değerlerini bir tamsayıya dönüştürülebileceği dizelere kısıtlar. Bir GET isteği `/api/test2/int/abc`:
  * Bu eylemle eşleşmiyor.
  * Bir [404 bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hatası döndürür.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` Eylem şablonda bulunur `{id}` , ancak bir tamsayıya dönüştürülemeyen `id` değerlerle sınırlandırmaz. Bir GET isteği `/api/test2/int2/abc`:
  * Bu rota ile eşleşir.
  * Model bağlama bir tamsayıya dönüştürülemez `abc` . Yönteminin `id` parametresi tamsayıdır.
  * Model bağlama bir tamsayıya dönüştürülemediğinden`abc` , [400 hatalı bir istek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> Öznitelik yönlendirme <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, ve <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>gibi öznitelikleri kullanabilir. Tüm [http fiili](#verb) öznitelikleri bir yol şablonunu kabul eder. Aşağıdaki örnekte, aynı yol şablonuyla eşleşen iki eylem gösterilmektedir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL yolu `/products3`kullanılarak:

* `MyProductsController.ListProducts` Eylem [http fiili](#verb) olduğunda çalışır `GET`.
* `MyProductsController.CreateProduct` Eylem [http fiili](#verb) olduğunda çalışır `POST`.

Bir REST API oluştururken, eylem tüm HTTP yöntemlerini kabul ettiğinden bir eylem yönteminde kullanmanız `[Route(...)]` gerekecektir. API 'nizin neleri desteklediği hakkında kesin olması için, daha özel [http fiili özniteliği](#verb) kullanmak daha iyidir. REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.

REST API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır. Bu, örneğin, aynı mantıksal kaynaktaki al ve postala gibi birçok işlemin aynı URL 'YI kullanması anlamına gelir. Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)` Eylem:

* , Şunun gibi URL yoluyla çalıştırılır`/products2/3`
* URL yoluyla `/products2`çalıştırılmadı.

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar. Daha fazla bilgi için bkz. [desteklenen istek içerik türlerini tüketir özniteliğiyle tanımlama](xref:web-api/index#consumes).

 Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .

Hakkında `[ApiController]`daha fazla bilgi için bkz. [Apicontroller özniteliği](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Yönlendirme adı

Aşağıdaki kod, bir yol adı tanımlar `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir. Yol adları:

* Yönlendirmenin URL eşleşen davranışı üzerinde hiçbir etkisi yoktur.
* Yalnızca URL oluşturma için kullanılır.

Yol adları, uygulama genelinde benzersiz olmalıdır.

Önceki kodu, `id` parametreyi isteğe bağlı (`{id?}`) olarak tanımlayan geleneksel varsayılan rotayla kontrast. API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi `/products` ve `/products/5` dağıtılması gibi avantajlara sahiptir.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Öznitelik yollarını birleştirme

Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir. Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir. Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Yukarıdaki örnekte:

* URL yolu `/products` eşleşiyor`ProductsApi.ListProducts`
* URL yolu `/products/5` eşleşiyor `ProductsApi.GetProduct(int)`.

Bu eylemlerin her ikisi de, `GET` `[HttpGet]` özniteliğiyle işaretlendiğinden yalnızca http ile eşleşir.

Bir eyleme uygulanan `/` veya `~/` Bu denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları. Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolları kümesiyle eşleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Aşağıdaki tabloda önceki koddaki `[Route]` öznitelikler açıklanmaktadır:

| Öznitelik               | İle birleştirir`[Route("Home")]` | Rota şablonunu tanımlar |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **Hayır** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Öznitelik yolu sırası

Yönlendirme bir ağaç oluşturur ve aynı anda tüm uç noktaları eşleştirir:

* Yol girdileri ideal bir sıralamaya yerleştirilmiş gibi davranır.
* En özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.

Örneğin, gibi `blog/search/{topic}` bir öznitelik yolu, gibi `blog/{*article}`bir öznitelik rotasına göre daha özgüdür. Daha `blog/search/{topic}` belirgin olduğundan, yolun önceliği daha yüksektir. [Geleneksel yönlendirmeyi](#cr)kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.

Öznitelik yolları <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> özelliği kullanarak bir sıra yapılandırabilir. Tüm Framework 'ün [yol öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dahil `Order` edilmiştir. Yollar, `Order` özelliğin artan sıralamasına göre işlenir. Varsayılan sıra `0`. Bir siparişi ayarlamadığı `Order = -1` rotalardan önce çalıştırmaları kullanarak bir rota ayarlama. Varsayılan yol sıralaması sonrasında `Order = 1` çalıştırmaları kullanarak bir rota ayarlama.

**Avoid** Uygulamasına bağlı kaçının `Order`. Bir uygulamanın URL 'SI alanı, doğru şekilde yönlendirmek için açık sıra değerleri gerektiriyorsa, bu durumda istemciler de kafa karıştırıcı olabilir. Genel olarak, öznitelik yönlendirme URL ile eşleşen doğru yolu seçer. URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, geçersiz kılma olarak bir yol adı kullanılması, `Order` özelliği uygulamadan daha basittir.

Her ikisi de eşleşen `/home`yolu tanımlayan aşağıdaki iki denetleyicisi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Yukarıdaki `/home` kodla istemek şuna benzer bir özel durum oluşturur:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Yol `Order` özniteliklerinden birine ekleme belirsizliği çözer:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Yukarıdaki kodla `/home` `HomeController.Index` bitiş noktasını çalıştırır. Öğesine `MyDemoController.MyIndex`ulaşmak için `/home/MyIndex`. **Not**:

* Yukarıdaki kod bir örnek veya kötü yönlendirme tasarımdır. `Order` Özelliği göstermek için kullanılmıştı.
* `Order` Özelliği yalnızca belirsizlik çözümleniyor, bu şablon eşleştirilemez. `[Route("Home")]` Şablonu kaldırmak daha iyi olacaktır.

Bkz. [Razor Pages yol ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) rota sıralaması hakkında bilgiler için Razor Pages.

Bazı durumlarda, belirsiz yollarla bir HTTP 500 hatası döndürülür. Hangi uç noktaların neden olduğunu görmek için [günlük kaydını](xref:fundamentals/logging/index) kullanın `AmbiguousMatchException`.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Yönlendirme şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]

Daha kolay olması için, öznitelik rotaları, aşağıdakilerden birine bir belirteç ekleyerek ayrılmış yol parametrelerine yönelik belirteç değişimini destekler:

* Köşeli ayraçlar:`[]`
* Küme ayraçları:`{}`

, Ve `[action]` `[controller]` belirteçleri `[area]`, yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Yukarıdaki kodda:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Eşleştir`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Eşleştir`/Products0/Edit/{id}`

Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir. Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Öznitelik rotaları de devralma ile birleştirilebilir. Bu, belirteç değiştirme ile güçlü bir şekilde birleştirilir. Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
Her eylem için benzersiz bir yol adı üretir.

Sabit belirteç `[` değiştirme sınırlayıcısıyla eşleştirmek için veya `]`karakteri tekrarlayarak (`[[` veya `]]`) bunu kaçış.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın

Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü, <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre transformatörü `SubscriptionManagement` rota değerini şu şekilde `subscription-management`değiştirir:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Şu şekilde bir uygulama modeli kuralıdır:

* Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.
* Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Önceki `ListAll` Yöntem eşleşir `/subscription-management/list-all`.

, `RouteTokenTransformerConvention` ' `ConfigureServices`Da bir seçeneği olarak kaydedilir.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Bilgi tanımı için bkz. [Mdıdn Web docs](https://developer.mozilla.org/docs/Glossary/Slug) for the başlık.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Birden çok öznitelik yolu

Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler. Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel yolun davranışını taklit etmek olur:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biri ile birleştiribileceği anlamına gelir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Tüm [http fiili](#verb) yol kısıtlamaları uygulanır `IActionConstraint`.

Uygulayan <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> birden çok yol özniteliği bir eyleme yerleştirildiğinde:

* Her eylem kısıtlaması, denetleyiciye uygulanan rota şablonuyla birlikte birleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Eylemlerde birden çok yolun kullanılması yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmanız daha iyidir. **Yalnızca** gerektiğinde, var olan istemcileri desteklemek için, eylemlerde birden çok yol kullanın.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme

Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Yukarıdaki kodda, `[HttpPost("product/{id:int}")]` bir yol kısıtlaması uygular. `ProductsController.ShowProduct` Eylem yalnızca, gibi `/product/3`URL yollarıyla eşleştirilir. Yol şablonu bölümü `{id:int}` , bu segmenti yalnızca tamsayılarla kısıtlar.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Iroutetemplateprovider kullanarak özel yol öznitelikleri

Tüm [Rota öznitelikleri](#rt) uygular <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. ASP.NET Core çalışma zamanı:

* Uygulama başladığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar.
* İlk yol kümesini oluşturmak için `IRouteTemplateProvider` uygulayan öznitelikleri kullanır.

Özel `IRouteTemplateProvider` yol özniteliklerini tanımlamak için uygulayın. Her `IRouteTemplateProvider` biri, özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Önceki `Get` Yöntem döndürülür `Order = 2, Template = api/MyTestApi`.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Öznitelik yollarını özelleştirmek için uygulama modelini kullanın

Uygulama modeli:

* , Başlangıçta oluşturulan bir nesne modelidir.
* Bir uygulamadaki eylemleri yönlendirmek ve yürütmek için ASP.NET Core tarafından kullanılan tüm meta verileri içerir.

Uygulama modeli, yol özniteliklerinden toplanan tüm verileri içerir. Yol özniteliklerinin verileri `IRouteTemplateProvider` uygulama tarafından sağlanır. Adlandır

* , Yönlendirmenin nasıl davranacağını özelleştirmek için uygulama modelini değiştirmek üzere yazılabilir.
* Uygulama başlangıcında okundu.

Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin temel bir örneği gösterilmektedir. Aşağıdaki kod, rotaları projenin klasör yapısıyla kabaca bir şekilde oluşturur.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Aşağıdaki kod, `namespace` kuralı yönlendirilen denetleyicilere uygulanmasını engeller:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Örneğin, aşağıdaki denetleyici kullanmaz `NamespaceRoutingConvention`:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` Yöntemi:

* Denetleyici öznitelik yönlendirmemişse hiçbir şey yapmaz.
* Temel `namespace` kaldırılmış olarak, denetleyici şablonunu öğesine `namespace`göre ayarlar.

, `NamespaceRoutingConvention` ' De `Startup.ConfigureServices`uygulanabilir:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Yukarıdaki kodda:

* Temel `namespace` `My.Application`.
* Önceki denetleyicinin tam adı `My.Application.Admin.Controllers.UsersController`.
* , `NamespaceRoutingConvention` Denetleyiciler şablonunu olarak `Admin/Controllers/Users/[action]/{id?`ayarlar.

, `NamespaceRoutingConvention` Bir denetleyiciye bir öznitelik olarak da uygulanabilir:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme

ASP.NET Core uygulamalar geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir. Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.

Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir. Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar. Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir. Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki **Tüm** eylemlerin yönlendirilmesini sağlar.

Öznitelik yönlendirme ve geleneksel yönlendirme aynı yönlendirme altyapısını kullanır.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL oluşturma ve ortam değerleri

Uygulamalar, eylemlere URL bağlantıları oluşturmak için yönlendirme URL 'SI oluşturma özelliklerini kullanabilir. URL 'Leri oluşturmak, kod daha sağlam ve sürdürülebilir hale getirmek için sorunsuz kodlama URL 'Lerini ortadan kaldırır. Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturma özelliğinin nasıl çalıştığına ilişkin temel bilgileri kapsar. URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ARABIRIM, URL oluşturma için MVC ve yönlendirme arasındaki altyapının temel öğesidir. Bir örneği `IUrlHelper` , denetleyiciler, görünümler ve `Url` görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir.

Aşağıdaki örnekte, `IUrlHelper` arabirimi başka bir eyleme bir URL oluşturmak için `Controller.Url` özelliği aracılığıyla kullanılır.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` DEĞIŞKENININ değeri URL yol dizesidir. `/UrlGeneration/Destination` Bu URL yolu, yönlendirme tarafından birleştirerek oluşturulur:

* Geçerli istekten, **ortam değerleri**olarak adlandırılan rota değerleri.
* Bu değerleri yol şablonuna `Url.Action` geçirilen ve yerine geçen değerler:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir. Bir değere sahip olmayan bir rota parametresi şunları yapabilir:

* Bir varsayılan değer varsa, bu değeri kullanın.
* İsteğe bağlı ise atlanır. Örneğin, `id` rota şablonundan `{controller}/{action}/{id?}`.

Herhangi bir gerekli yol parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur. Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.

Yukarıdaki örnekte `Url.Action` [geleneksel yönlendirme](#cr)varsayılır. URL oluşturma, [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır, ancak kavramlar farklıdır. Geleneksel yönlendirme ile:

* Rota değerleri bir şablonu genişletmek için kullanılır.
* İçin `controller` yol değerleri ve `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme ile eşleşen URL 'Ler bir kurala bağlı olduğundan, bu işe yarar.

Aşağıdaki örnek öznitelik yönlendirme kullanır:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Yukarıdaki `Source` koddaki eylem oluşturulur `custom/url/to/destination`.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>ASP.NET Core 3,0 ' ye `IUrlHelper`alternatif olarak eklenmiştir. `LinkGenerator`benzer ancak daha esnek işlevler sunar. Üzerindeki `IUrlHelper` her bir yöntemi, buna karşılık gelen bir yöntem `LinkGenerator` ailesini de içerir.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL 'Leri oluşturma

[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirtilerek hedef uç noktası oluşturmak için tasarlanmıştır.

Kullanırken `Url.Action`, ve `controller` `action` için geçerli yol değerleri çalışma zamanı tarafından sağlanır:

* `controller` Ve `action` değeri hem [ortam değerlerinin](#ambient) hem de değerlerinin bir parçasıdır. Yöntemi `Url.Action` her zaman `action` ve `controller` geçerli değerlerini KULLANıR ve geçerli eyleme yönlendiren bir URL yolu oluşturur.

Yönlendirme, bir URL oluştururken sağlanmayan bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmaya çalışır. Ortam değerleriyle `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }`benzer bir yol düşünün:

* Yönlendirmenin ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.
* Tüm rota parametrelerinin bir değeri olduğundan, yönlendirmeye yeterli bilgi vardır.

Değer `{ d = Donovan }` eklenirse:

* Değer `{ d = David }` yok sayılır.
* Oluşturulan URL yolu `Alice/Bob/Carol/Donovan`.

**Uyarı**: URL yolları hiyerarşiktir. Yukarıdaki örnekte, değer `{ c = Cheryl }` eklenirse:

* Her iki değer `{ c = Carol, d = David }` de yok sayılır.
* Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.
* İçin istenen değerleri `c` ve `d` bir URL oluşturmak için belirtilmelidir.  

Varsayılan yol `{controller}/{action}/{id?}`ile bu soruna isabet etmeniz gerekebilir. `Url.Action` Her zaman açıkça bir `controller` ve `action` değeri belirttiğinden bu sorun pratikte nadir olarak belirlenir.

URL 'nin çeşitli aşırı yüklemeleri [. eylem](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) , `controller` ve `action`dışındaki rota parametreleri için değerler sağlamak üzere bir yol değerleri nesnesi alır. Route Values nesnesi genellikle ile birlikte `id`kullanılır. Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`. Yol değerleri nesnesi:

* Kural gereği genellikle anonim türdeki bir nesnedir.
* Bir `IDictionary<>` veya [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).

Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Yukarıdaki kod oluşturulur `/Products/Buy/17?color=red`.

Aşağıdaki kod mutlak URL oluşturur:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Mutlak URL oluşturmak için, aşağıdakilerden birini kullanın:

* Kabul eden bir `protocol`aşırı yükleme. Örneğin, yukarıdaki kod.
* Varsayılan olarak mutlak URI 'Ler üreten [Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*).

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Yola göre URL oluşturma

Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir. `IUrlHelper`Ayrıca [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) ailesi yöntemlerin de sağlar. Bu yöntemler [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)ile benzerdir, ancak geçerli değerlerini `action` ve `controller` rota değerlerine kopyalamaz. En yaygın kullanımı `Url.RouteUrl`:

* URL oluşturmak için bir yol adı belirtir.
* Genellikle bir denetleyici veya eylem adı belirtmez.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Aşağıdaki Razor dosyası bir HTML bağlantısı oluşturur `Destination_Route`:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>HTML ve Razor 'de URL oluşturma

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>sırasıyla ve <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> `<a>` öğeleri oluşturmak `<form>` Için [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini sağlar. Bu yöntemler bir URL oluşturmak için [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler. İçin `Url.RouteUrl` `HtmlHelper` şirkeme `Html.BeginRouteForm` , ve `Html.RouteLink` benzer işlevlere sahiptir.

Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur. Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır. Daha fazla bilgi için bkz. [Formlardaki etiket yardımcıları](xref:mvc/views/working-with-forms) .

Görünümler `IUrlHelper` içinde, yukarıda yer almayan herhangi BIR `Url` geçici URL oluşturma özelliği aracılığıyla kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Eylem sonuçlarında URL oluşturma

Yukarıdaki örneklerde bir denetleyicide `IUrlHelper` kullanılması gösterildi. Bir denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak bir URL oluşturmak olur.

Ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar. Kullanıcı girişini kabul ettikten sonra, yaygın olarak kullanılan bir kullanım yeniden yönlendirilmelidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Eylem, gibi Fabrika yöntemleri sonuçları <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> üzerindeki `IUrlHelper`yöntemlere benzer bir model izler.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Adanmış geleneksel yollar için özel durum

[Geleneksel yönlendirme](#cr) , [adanmış geleneksel yol](#dcr)olarak adlandırılan özel bir yol tanımı türünü kullanabilir. Aşağıdaki örnekte, adlı `blog` yol ayrılmış bir geleneksel yoldur:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Önceki yol tanımlarını kullanarak, `Url.Action("Index", "Home")` `/` `default` yolu kullanarak URL yolunu oluşturur, ancak neden? Yol değerlerinin `{ controller = Home, action = Index }` bir URL `blog`oluşturmak için yeterli olacağını tahmin edebilirsiniz ve sonuç olur. `/blog?action=Index&controller=Home`

[Adanmış geleneksel yollar](#dcr) , karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır ve bu da yol, URL oluşturma ile çok fazla [dodan](xref:fundamentals/routing#greedy) yararlanın. Bu durumda `{ controller = Blog, action = Article }`, varsayılan değerler, `controller` ya `action` da yol parametresi olarak görünmez. Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir. Değerler `{ controller = Home, action = Index }` eşleşmediğinden `{ controller = Blog, action = Article }`URL `blog` oluşturma başarısız olur. Sonra yeniden yönlendirme işlemi denemeye `default`geri döner ve başarılı olur.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Bölgeler](xref:mvc/controllers/areas) , ilgili işlevselliği ayrı olarak bir grup içinde düzenlemek için kullanılan bir MVC özelliğidir:

* Denetleyici eylemleri için yönlendirme ad alanı.
* Görünümler için klasör yapısı.

Alanların kullanılması, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar. Alanların kullanılması, ve `area` `controller` `action`' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi oluşturur. Bu bölümde yönlendirmenin alanlarla nasıl etkileşim kurduğu açıklanmaktadır. Alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](xref:mvc/controllers/areas) bakın.

Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve bir `area` `area` adlandırılmış `Blog`yolu kullanacak şekilde yapılandırır:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Önceki kodda, öğesini <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> oluşturmak için çağırılır. `"blog_route"` İkinci parametresi, `"Blog"`, alan adıdır.

Benzer `/Manage/Users/AddUser`bir URL yolu eşleştirilirken `"blog_route"` yol, yol değerlerini `{ area = Blog, controller = Users, action = AddUser }`oluşturur. `area` Rota değeri, için `area`varsayılan bir değer tarafından üretilir. Tarafından `MapAreaControllerRoute` oluşturulan yol, aşağıdaki ile eşdeğerdir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`Bu durumda `area` `Blog`, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur. Varsayılan değer, yolun her zaman oluşturulmasını sağlar `{ area = Blog, ... }`, kısıtlama URL oluşturma için değeri `{ area = Blog, ... }` gerektirir.

Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.

Önceki örneği kullanarak, yol değerleri `{ area = Blog, controller = Users, action = AddUser }` aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir alanın parçası olarak denetleyiciyi belirtir. Bu denetleyici `Blog` alanında bulunur. Özniteliği olmayan denetleyiciler hiçbir alanın üyesi değildir ve `area` rota değeri yönlendirme tarafından sağlandığında **eşleşmez.** `[Area]` Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle `{ area = Blog, controller = Users, action = AddUser }`eşleştirebilir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Her denetleyicinin ad alanı, tamamlanma açısından burada gösterilmiştir. Yukarıdaki denetleyiciler aynı ad alanını kullanıyorsa, bir derleyici hatası oluşturulur. Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı `area` rota değeri tarafından sağlandığında eşleşir. Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer `area` sağlanmamışsa eşleşemez.

<a name="aa"></a>

Değer *olmadan*eşleşme açısından `area` değerin yokluğu değeri null ya da boş dize olarak `area` aynıdır.

Bir alan içinde bir eylem yürütürken, için `area` rota değeri, yönlendirme IÇIN, URL oluşturma için kullanılacak bir [çevresel değer](#ambient) olarak kullanılabilir. Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Aşağıdaki kod, için `/Zebra/Users/AddUser`bir URL oluşturur:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Eylem tanımı

Bir denetleyicide, [Nonactıon](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özniteliğine sahip olanlar hariç genel yöntemler eylemlerdir.

## <a name="sample-code"></a>Örnek kod

 * [Mydisplayrouteınfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) yöntemi [örnek karşıdan yüklemeye](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) dahildir ve yönlendirme bilgilerini görüntülemek için kullanılır.
* [Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır. Yollar başlangıç kodunda veya özniteliklerde tanımlanmıştır. Yollar URL yollarının eylemlerle nasıl eşleştirileceği açıklanır. Yollar, yanıt olarak gönderilen URL 'Leri (bağlantılar için) oluşturmak için de kullanılır.

Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir. Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar. Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .

Bu belge, MVC ve yönlendirme arasındaki etkileşimleri ve tipik MVC uygulamalarının yönlendirme özelliklerini nasıl kullandığını açıklar. Gelişmiş yönlendirme hakkında ayrıntılar için bkz. [yönlendirme](xref:fundamentals/routing) .

## <a name="setting-up-routing-middleware"></a>Yönlendirme ara yazılımını ayarlama

*Yapılandırma* yönteminde şuna benzer bir kod görebilirsiniz:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Çağrısı içinde `UseMvc`, `MapRoute` `default` yolu olarak başvurabileceğiniz tek bir yol oluşturmak için kullanılır. Çoğu MVC uygulaması, `default` yola benzer bir şablon içeren bir yol kullanır.

Yol şablonu `"{controller=Home}/{action=Index}/{id?}"` , gıbı `/Products/Details/5` bir URL yoluyla eşleşir ve yolu simgeleştirileyerek yol değerlerini `{ controller = Products, action = Details, id = 5 }` ayıklar. MVC adlı `ProductsController` bir denetleyiciyi bulmayı dener ve bu eylemi `Details`çalıştırır:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Bu örnekte, model bağlamanın bu eylemi çağırırken `id = 5` `id` parametresini olarak `5` ayarlamak için değerini kullanacağı unutulmamalıdır. Daha fazla ayrıntı için [model bağlamaya](../models/model-binding.md) bakın.

`default` Yolu kullanarak:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Yol şablonu:

* `{controller=Home}`Varsayılan `Home` olarak tanımlar`controller`

* `{action=Index}`Varsayılan `Index` olarak tanımlar`action`

* `{id?}`isteğe `id` bağlı olarak tanımlar

Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez. Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .

`"{controller=Home}/{action=Index}/{id?}"`URL yoluyla `/` eşleştirebilir ve yol değerlerini `{ controller = Home, action = Index }`oluşturacaktır. , URL yolunda `controller` karşılık `action` gelen bir kesim olmadığından, için değerleri `id` ve varsayılan değerleri kullanır, bir değer üretmez. MVC, `HomeController` ve `Index` eylemini seçmek için bu yol değerlerini kullanır:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Bu denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem aşağıdaki URL yollarından herhangi biri için yürütülür:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Kolaylık yöntemi `UseMvcWithDefaultRoute`:

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

`UseMvc`ve `UseMvcWithDefaultRoute` , ara yazılım ardışık `RouterMiddleware` düzenine bir örneği ekleyin. MVC, doğrudan ara yazılım ile etkileşime girmez ve istekleri işlemek için yönlendirmeyi kullanır. MVC, bir örneği aracılığıyla yollara bağlanır `MvcRouteHandler`. İçindeki `UseMvc` kod şuna benzer:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`hiçbir yol doğrudan tanımlamaz, yol için `attribute` yol koleksiyonuna bir yer tutucu ekler. Aşırı yükleme `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.  `UseMvc`ve tüm varyasyonları, yapılandırma `UseMvc`şeklinden bağımsız olarak her zaman için öznitelik rotası özniteliği yönlendirme özelliği için bir yer tutucu ekler. `UseMvcWithDefaultRoute`Varsayılan bir yol tanımlar ve öznitelik yönlendirmeyi destekler. [Öznitelik yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Geleneksel yönlendirme

`default` Yol:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Yukarıdaki kod geleneksel yönlendirmeye bir örnektir. Bu stil, URL yolları için bir *kural* oluşturduğundan geleneksel yönlendirme olarak adlandırılır:

* İlk yol segmenti, denetleyicinin adıyla eşlenir.
* İkincisi eylem adıyla eşlenir.
* Üçüncü segment, isteğe bağlı `id`olarak kullanılır. `id`bir model varlığına eşlenir.

`default` Bu yolu `/Products/List` kullanarak, URL yolu `ProductsController.List` eyleme eşlenir ve `/Blog/Article/17` ile `BlogController.Article`eşlenir. Bu eşleme **yalnızca** denetleyiciye ve eylem adlarına dayalıdır ve ad alanları, kaynak dosya konumları veya yöntem parametrelerine göre değildir.

> [!TIP]
> Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, tanımladığınız her eylem için yeni bir URL düzeniyle karşılaşmanıza gerek kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır. CRUD stilinde eylemlere sahip bir uygulama için denetleyicilerinizdeki URL 'Lerin tutarlılığı, kodunuzun basitleştirilmesine ve Kullanıcı arabiriminizi daha öngörülebilir hale getirmenize yardımcı olabilir.

> [!WARNING]
> , `id` Yol şablonu tarafından isteğe bağlı olarak tanımlanır ve bu, EYLEMLERINIZIN URL 'nin bir parçası olarak sağlanmadan yürütebileceği anlamına gelir. Genellikle URL 'den atlanırsa ne `id` olacağı, model bağlama `0` tarafından ayarlanabileceği ve sonuç olarak veritabanı eşleştirmesinin `id == 0`hiçbir varlık bulunamadığı durumlarda gerçekleşir. Öznitelik yönlendirme, bazı eylemler için gereken KIMLIĞI, diğerleri için değil, daha ayrıntılı bir denetim sağlayabilir. Kurala göre belgeler, doğru kullanımda görünebilecekleri gibi `id` isteğe bağlı parametreleri de içerecektir.

## <a name="multiple-routes"></a>Birden çok yol

Uygulamasına `MapRoute`daha fazla çağrı ekleyerek içine `UseMvc` birden çok yol ekleyebilirsiniz. Bunun yapılması, birden çok kural tanımlamanızı veya belirli bir eyleme adanmış geleneksel yollar eklemenizi sağlar; örneğin:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Burada `blog` yol, geleneksel bir *geleneksel yol*olduğundan geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme ayrılmıştır. `controller` Yol şablonunda `action` parametre olarak görünmeyin, ancak bu yol yalnızca varsayılan değerlere sahip olabilir ve bu nedenle bu yol her zaman eyleme `BlogController.Article`eşlenir.

Rota koleksiyonundaki yollar sıralanır ve eklendikleri sırada işlenir. Bu nedenle bu örnekte `blog` yol, `default` yol öncesinde denenmeye sunulacaktır.

> [!NOTE]
> *Adanmış geleneksel yollar* genellıkle, URL yolunun kalan kısmını yakalamak için `{*article}` gibi **catch-all** yol parametrelerini kullanır. Bu, ' çok Greedy ' yolunu diğer yollarla eşleştirirken hedeflediğiniz URL 'Lerle eşleşen bir yol haline getirir. Bunu çözümlemek için ' Greedy ' yollarını daha sonra yol tablosuna koyun.

### <a name="fallback"></a>Geri dönüş

İstek işlemenin bir parçası olarak, MVC, uygulamanızdaki bir denetleyiciyi ve eylemi bulmak için yol değerlerinin kullanılabileceğini doğrular. Rota değerleri bir eylemle eşleşmezse, yol eşleşme olarak kabul edilmez ve sonraki rota denenir. Buna *geri dönüş*denir ve geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.

### <a name="disambiguating-actions"></a>Kesinleştirme eylemleri

İki eylem yönlendirme aracılığıyla eşleşiyorsa, MVC ' en iyi ' adayı seçmek için bir özel durum oluşturması veya bir özel durum oluşturmak için, MVC 'nin belirsizliğini Örneğin:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Bu denetleyici, URL yolu `/Products/Edit/17` ve rota verileri `{ controller = Products, action = Edit, id = 17 }`ile eşleşen iki eylemi tanımlar. Bu, bir ürünü düzenlemek için bir form `Edit(int)` gösteren ve `Edit(int, Product)` postalanan formu işleyen MVC denetleyicileri için tipik bir modeldir. Bunu yapmak için, istek bir HTTP `Edit(int, Product)` `POST` olduğunda ve `Edit(int)` http fiili başka bir şey olduğunda bu olası MVC 'nin seçim yapması gerekir.

( `HttpPostAttribute` `[HttpPost]` ), Yalnızca http fiili olduğunda `IActionConstraint` eylemin seçili olmasını sağlayan bir uygulamasıdır `POST`. ' Nin varlığı ' `IActionConstraint` daha iyi `Edit(int, Product)` ' bir eşleşme yapar `Edit(int)`, bu nedenle `Edit(int, Product)` ilk olarak denenir.

Yalnızca özelleştirilmiş senaryolarda özel `IActionConstraint` uygulamalar yazmanız gerekir, ancak benzer öznitelikler gibi `HttpPostAttribute` özniteliklerin rolün diğer HTTP fiilleri için tanımlandığını anlamak önemlidir. Geleneksel yönlendirmesinde, bir iş akışının parçası olduklarında aynı eylem adını kullanmak yaygın olarak karşılaşılan bir `show form -> submit form` işlemdir. Bu düzenin rahatlığı, [ıactionconstraint 'ı anlama](#understanding-iactionconstraint) bölümünde daha sonra görünür hale gelir.

Birden çok yol eşleşirse ve MVC ' en iyi ' yolu bulamazsa, bir `AmbiguousActionException`oluşturur.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Yol adları

Dizeler `"blog"` ve `"default"` aşağıdaki örneklerde yol adları verilmiştir:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Yol adları, URL oluşturma için adlandırılmış yolun kullanılabilmesi için yola mantıksal bir ad verir. Bu, yolların sıralaması URL oluşturma karmaşık hale geldiğinde URL oluşturmayı büyük ölçüde basitleştirir. Yol adları, uygulama genelinde benzersiz olmalıdır.

Yol adları, isteklerin URL 'SI ile eşleşmesini veya işlenmesini etkilemez; Bunlar yalnızca URL oluşturma için kullanılır. [Yönlendirme](xref:fundamentals/routing) , MVC 'ye özgü yardımcılardaki URL oluşturma da dahil olmak üzere URL oluşturma hakkında daha ayrıntılı bilgiler içerir.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Öznitelik yönlendirme

Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır. Aşağıdaki örnekte, `app.UseMvc();` `Configure` yönteminde kullanılır ve hiçbir yol geçirilir. `HomeController` Varsayılan yolun `{controller=Home}/{action=Index}/{id?}` eşleşeceğini benzer bir URL kümesiyle eşleşir:

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

Bu `HomeController.Index()` eylem, veya `/` `/Home` `/Home/Index`URL yollarının herhangi biri için yürütülür.

> [!NOTE]
> Bu örnek, öznitelik yönlendirme ve geleneksel yönlendirme arasında bir temel programlama farkı vurgulamaktadır. Öznitelik yönlendirme, bir yol belirtmek için daha fazla giriş gerektirir; geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler. Ancak, öznitelik yönlendirme (ve gerektirir) her eylem için hangi rota şablonlarının uygulanacağını kesin olarak denetler.

Öznitelik yönlendirme ile, denetleyici adı ve eylem adları, **herhangi** bir eylem seçildiği hiçbir rol oynar. Bu örnek, önceki örnekle aynı URL 'Lerle eşleştirecektir.

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
> Yukarıdaki yol şablonları, `action` `area`, ve `controller`için yol parametreleri tanımlamaz. Aslında, öznitelik rotalarında bu yol parametrelerine izin verilmez. Yol şablonu bir eylemle zaten ilişkili olduğundan, URL 'den eylem adını ayrıştırmak mantıklı değildir.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http [fiil] öznitelikleriyle öznitelik yönlendirme

Öznitelik yönlendirme, `Http[Verb]` gibi öznitelikleri de kullanabilir `HttpPostAttribute`. Bu özniteliklerin hepsi bir yol şablonunu kabul edebilir. Bu örnekte, aynı rota şablonuyla eşleşen iki eylem gösterilmektedir:

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

Bir URL yolu için, `/products` http `ProductsApi.ListProducts` fiili olduğunda `GET` ve `ProductsApi.CreateProduct` http fiili olduğunda yürütülecektir, eylem gibi bir URL yolu yürütülür `POST`. Öznitelik yönlendirme öncelikle URL ile yol öznitelikleri tarafından tanımlanan yol şablonları kümesine göre eşleşir. Bir rota şablonu eşleştiğinde, `IActionConstraint` hangi eylemlerin yürütüleceğini belirleyen kısıtlamalar uygulanır.

> [!TIP]
> Bir REST API oluştururken, eylem tüm HTTP yöntemlerini kabul edecek şekilde bir eylem yönteminde `[Route(...)]` kullanmak isteyeceksiniz. API 'nizin neleri desteklediği hakkında kesin olması için `Http*Verb*Attributes` daha belirgin bir şekilde kullanılması daha iyidir. REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.

Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Eylem `ProductsApi.GetProduct(int)` , gibi bir URL yolu için değil, gıbı `/products/3` bir URL yolu için yürütülür `/products`. Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .

## <a name="route-name"></a>Yol adı

Aşağıdaki kod, bir *yol adı* tanımlar `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir. Rota adlarının, yönlendirmenin URL eşleştirme davranışına etkisi yoktur ve yalnızca URL oluşturma için kullanılır. Yol adları, uygulama genelinde benzersiz olmalıdır.

> [!NOTE]
> Bunu, isteğe bağlı ( `id` `{id?}`) parametresini tanımlayan geleneksel *varsayılan yol*ile kontrast. API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi `/products` ve `/products/5` dağıtılması gibi avantajlara sahiptir.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Yolları birleştirme

Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir. Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir. Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.

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

Bu örnekte, URL `/products` yolu eşleştirebilir `ProductsApi.ListProducts`ve URL yolu `/products/5` eşleştirebilir. `ProductsApi.GetProduct(int)` Bu eylemlerin her ikisi de ile işaretlendikleri için `GET` `HttpGetAttribute`http ile eşleşir.

Bir eyleme uygulanan `/` veya `~/` Bu denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları. Bu örnek, *varsayılan rotaya*benzer bir URL yolları kümesiyle eşleşir.

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

### <a name="ordering-attribute-routes"></a>Öznitelik yollarını sıralama

Tanımlı bir düzende yürütülen geleneksel yolların aksine, öznitelik yönlendirme bir ağaç oluşturur ve tüm yollarla aynı anda eşleşir. Bu, yol girişleri ideal bir sıralamaya yerleştirildiyse olduğu gibi davranır; en özel yolların, daha genel yollardan önce yürütülmesi şansınız vardır.

Örneğin, gibi `blog/search/{topic}` bir yol gibi `blog/{*article}`bir yol daha özeldir. Tek yapmanız gereken `blog/search/{topic}` tek bir sıralama olduğundan, varsayılan olarak ' çalıştırmaları ' yolunu mantıksal olarak konuşuyor. Geleneksel yönlendirmeyi kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.

Öznitelik rotaları, tüm Framework yol özniteliklerinin `Order` özelliğini kullanarak bir sıra yapılandırabilir. Yollar, `Order` özelliğin artan sıralamasına göre işlenir. Varsayılan sıra `0`. Kullanarak `Order = -1` bir yolun ayarlanması, bir sipariş ayarlamamadan önce çalışır. Kullanarak `Order = 1` bir yolun ayarlanması, varsayılan yol sıralaması sonrasında çalışır.

> [!TIP]
> Uygulamasına bağlı kaçının `Order`. URL alanınız, doğru sıralama değerlerinin doğru şekilde yönlendirilmesini gerektiriyorsa, istemciler de kafa karıştırıcı olabilir. Genel öznitelik yönlendirme ' de, URL eşleştirme ile doğru yolu seçer. URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, bir geçersiz kılma olarak yol adı kullanılması, `Order` özelliği uygulamadan daha basittir.

Razor Pages yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. Razor Pages konularındaki yol siparişi hakkında bilgiler [Razor Pages yol ve uygulama kuralları: yol sıralaması](xref:razor-pages/razor-pages-conventions#route-order)' nda bulunabilir.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Yol şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])

Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (`[`, `]`) içine alarak *belirteç değiştirmeyi* destekler. , Ve `[action]` `[controller]` belirteçleri `[area]`, yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir. Aşağıdaki örnekte, Eylemler, açıklamalarda açıklandığı gibi URL yollarıyla eşleşir:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir. Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Öznitelik rotaları de devralma ile birleştirilebilir. Bu özellikle, belirteç değiştirme ile güçlü bir şekilde birleştirilir.

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

Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir. `[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir.

Sabit belirteç `[` değiştirme sınırlayıcısıyla eşleştirmek için veya `]`karakteri tekrarlayarak (`[[` veya `]]`) bunu kaçış.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın

Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü, `IOutboundParameterTransformer` parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel `SlugifyParameterTransformer` bir parametre transformatörü `SubscriptionManagement` rota değerini olarak `subscription-management`değiştirir.

, `RouteTokenTransformerConvention` Şu şekilde bir uygulama modeli kuralıdır:

* Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.
* Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

, `RouteTokenTransformerConvention` ' `ConfigureServices`Da bir seçeneği olarak kaydedilir.

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

### <a name="multiple-routes"></a>Birden çok yol

Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler. Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi *varsayılan geleneksel yolun* davranışını taklit etmek olur:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biriyle birleşmesi anlamına gelir.

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

Birden çok yol özniteliği (uygulayan `IActionConstraint`) bir eyleme yerleştirildiğinde, her eylem kısıtlaması, onu tanımlayan öznitelikten yol şablonuyla birleştirir.

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
> Eylemlerde birden çok yolun kullanılması güçlü görünse de, uygulamanızın URL alanının basit ve iyi tanımlanmış tutulması daha iyidir. Yalnızca gerektiğinde eylemler üzerinde birden çok yol kullanın, örneğin mevcut istemcileri desteklemek için.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme

Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Kullanarak özel yol öznitelikleri`IRouteTemplateProvider`

Çerçevede ( `[Route(...)]`, `[HttpGet(...)]` , vb.) sunulan tüm rota öznitelikleri, `IRouteTemplateProvider` arabirimini uygular. MVC, uygulama başlatıldığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar ve ilk rota kümesini oluşturmak için uygulamalarını `IRouteTemplateProvider` kullanır.

Kendi yol öznitelerinizi tanımlamak `IRouteTemplateProvider` için öğesini uygulayabilirsiniz. Her `IRouteTemplateProvider` biri, özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Yukarıdaki örnekteki özniteliği, `Template` `"api/[controller]"` ne zaman `[MyApiController]` uygulanacağını otomatik olarak olarak ayarlar.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Öznitelik yollarını özelleştirmek için uygulama modelini kullanma

*Uygulama modeli* , MVC tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta veriler ile başlangıçta oluşturulan bir nesne modelidir. *Uygulama modeli* , yol özniteliklerinden (aracılığıyla `IRouteTemplateProvider`) toplanan tüm verileri içerir. Yönlendirme işleminin nasıl davranacağını özelleştirmek için, Başlangıç zamanında uygulama modelini değiştirmek üzere *kurallar* yazabilirsiniz. Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin basit bir örneği gösterilmektedir.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme

MVC uygulamaları, geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir. Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.

Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir. Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar. Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir. Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemlerin yönlendirilmesini sağlar.

> [!NOTE]
> İki tür yönlendirme sisteminin ayırt edilmesini ne kadar ayırt eden, bir URL bir yol şablonuyla eşleştirdikten sonra uygulanan işlemdir. Geleneksel yönlendirmesinde, eşleşmeden yol değerleri, tüm geleneksel yönlendirilmiş eylemlerin arama tablosundan eylemi ve denetleyiciyi seçmek için kullanılır. Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilendirilir ve başka bir arama gerekmez.

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]`), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir. Bir açıklama için bkz. [kaynak kodu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) . Daha fazla bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL oluşturma

MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir. URL oluşturma, kodlarınızın daha sağlam ve sürdürülebilir hale getirilmesi için sorunsuz kodlama URL 'Lerini ortadan kaldırır. Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar. URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .

`IUrlHelper` ARABIRIM, URL oluşturma için MVC ve yönlendirme arasındaki temel altyapı parçasıdır. Denetleyiciler, görünümler ve görünüm bileşenlerinde `IUrlHelper` `Url` özelliği aracılığıyla kullanılabilir bir örnek bulacaksınız.

Bu örnekte `IUrlHelper` arabirim, başka bir eyleme YÖNELIK bir URL `Controller.Url` oluşturmak için özelliği aracılığıyla kullanılır.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` DEĞIŞKENIN değeri URL yol dizesi `/UrlGeneration/Destination`olacaktır. Bu URL yolu, yönlendirme değerlerini, geçerli istekten (çevresel değerler), bu değerleri geçirilen değerlerle `Url.Action` ve bu değerlerin yol şablonuna geçirerek birleştirerek oluşturulur.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir. Bir değere sahip olmayan bir rota parametresi, varsa varsayılan bir değer kullanabilir veya isteğe bağlı ise (Bu örnekte olduğu gibi `id` ) atlanır. Gerekli yol parametresinin karşılık gelen bir değeri yoksa, URL oluşturma başarısız olur. Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.

`Url.Action` Yukarıdaki örneği geleneksel yönlendirmeyi varsayar, ancak URL oluşturma özniteliği farklı olsa da, öznitelik yönlendirme ile benzer şekilde çalışır. Geleneksel yönlendirme ile, bir şablonu genişletmek için yol değerleri kullanılır ve `controller` `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme ile eşleşen URL 'ler bir *kurala*bağlı olduğundan, bu işe yarar. Öznitelik yönlendirmesinde, ve `controller` `action` için rota değerlerinin şablonda görünmesine izin verilmez; bunun yerine kullanılacak şablonu aramak için kullanılır.

Bu örnek öznitelik yönlendirme kullanır:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC, `controller` tüm öznitelik yönlendirilmiş eylemlerinin bir arama tablosunu oluşturur ve URL oluşturma için kullanılacak `action` yol şablonunu seçmek için ve değerleriyle eşleştirecektir. Yukarıdaki `custom/url/to/destination` örnekte oluşturulur.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL 'Leri oluşturma

`Url.Action` (`IUrlHelper` . `Action`) ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirterek ne bağlandığınızı belirtmek istediğiniz fikri temel alır.

> [!NOTE]
> Kullanırken `Url.Action`, `controller` için geçerli yol değerleri ve `action` değeri için belirtilir `controller` ve `action` her iki *çevresel değerin* **ve** *değerin*bir parçasıdır. Yöntemi `Url.Action`her zaman ve `action` `controller` ' nin geçerli değerlerini KULLANıR ve geçerli eyleme yönlendiren bir URL yolu oluşturacaktır.

Yönlendirme, bir URL oluştururken sağlamadığınız bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmayı dener. Yönlendirme ve ortam değerleri `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }`gibi bir yol kullanarak, yönlendirmenin tüm rota parametreleri bir değere sahip olduğundan, ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır. Değeri `{ d = Donovan }`eklediyseniz değer `{ d = David }` yok sayılır ve oluşturulan URL yolu da olur. `Alice/Bob/Carol/Donovan`

> [!WARNING]
> URL yolları hiyerarşiktir. Yukarıdaki örnekte, değerini `{ c = Cheryl }`eklediyseniz, her iki değer `{ c = Carol, d = David }` de yok sayılır. Bu durumda artık için `d` bir değer yoktur ve URL oluşturma başarısız olur. İstenen değerini `c` ve ' i `d`belirtmeniz gerekir.  Varsayılan yol`{controller}/{action}/{id?}`() ile bu soruna yaklaşmanız gerekebilir, ancak her zaman açıkça `Url.Action` bir `controller` ve `action` değeri belirtmek için uygulamada bu davranış hakkında nadiren karşılaşacaksınız.

Daha uzun aşırı `Url.Action` yüklemeleri Ayrıca, `controller` ve `action`dışındaki rota parametreleri için değerler sağlamak üzere ek bir *yol değerleri* nesnesi de alır. Bunun en yaygın olarak bu `id` şekilde `Url.Action("Buy", "Products", new { id = 17 })`kullanıldığını görürsünüz. Kurala göre *yol değerleri* nesnesi genellikle anonim türdeki bir nesnedir, ancak bir `IDictionary<>` veya düz olarak *eski .net nesnesi*de olabilir. Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Mutlak URL oluşturmak için şunu kabul eden bir aşırı yükleme kullanın `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Rotaya göre URL oluşturma

Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir. `IUrlHelper`Ayrıca Yöntem `Url.RouteUrl` ailesini de sağlar. Bu yöntemler öğesine `Url.Action`benzerdir, ancak geçerli değerlerini `action` ve `controller` yol değerlerini kopyalamalardır. En yaygın kullanım, genellikle bir denetleyici veya eylem *adı belirtmeden,* URL oluşturmak için belirli bir yolu kullanmak üzere bir yol adı belirtmektir.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML 'de URL oluşturma

`IHtmlHelper`, `HtmlHelper` `Html.BeginForm` ve `Html.ActionLink` `<form>` öğelerini sırasıyla oluşturmak ve oluşturmak için yöntemler sağlar. `<a>` Bu yöntemler bir URL `Url.Action` oluşturmak için yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler. İçin `Url.RouteUrl` `HtmlHelper` şirkeme `Html.BeginRouteForm` , ve `Html.RouteLink` benzer işlevlere sahiptir.

Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur. Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır. Daha fazla bilgi için bkz. [formlarla çalışma](../views/working-with-forms.md) .

Görünümler `IUrlHelper` içinde, yukarıda yer almayan herhangi BIR `Url` geçici URL oluşturma özelliği aracılığıyla kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Eylem sonuçlarında URL oluşturma

Yukarıdaki örnekler bir denetleyicide kullanılarak `IUrlHelper` gösteriliyor, ancak denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak bir URL oluşturmak olur.

Ve `ControllerBase` `Controller` temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar. Tipik bir kullanım, Kullanıcı girişi kabul edildikten sonra yeniden yönlendirilmelidir.

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

Eylem sonuçları Fabrika yöntemleri, üzerindeki `IUrlHelper`yöntemlere benzer bir model izler.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Adanmış geleneksel yollar için özel durum

Geleneksel yönlendirme, *adanmış geleneksel yol*olarak adlandırılan özel bir yol tanımı türünü kullanabilir. Aşağıdaki örnekte, adlı `blog` yol ayrılmış bir geleneksel yoldur.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Bu yol tanımlarının `Url.Action("Index", "Home")` kullanılması, `/` `default` yol ile URL yolunu oluşturacak, ancak neden? Yol değerlerinin `{ controller = Home, action = Index }` bir URL `blog`oluşturmak için yeterli olacağını tahmin edebilirsiniz ve sonuç olur. `/blog?action=Index&controller=Home`

Adanmış geleneksel yollar, URL oluşturmayla "çok Greedy" olmasını önleyen karşılık gelen bir yol parametresi olmayan varsayılan değerlerin özel bir davranışına bağımlıdır. Bu durumda `{ controller = Blog, action = Article }`, varsayılan değerler, `controller` ya `action` da yol parametresi olarak görünmez. Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir. Değerler `{ controller = Home, action = Index }` eşleşmediğinden `{ controller = Blog, action = Article }`, `blog` kullanılarak URL oluşturma başarısız olur. Sonra yeniden yönlendirme işlemi denemeye `default`geri döner ve başarılı olur.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Bölgeler](areas.md) , ilgili işlevselliği ayrı bir yönlendirme-ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir grupla düzenlemek için kullanılan bir MVC özelliğidir. Alanların kullanılması, bir uygulamanın farklı *alanlara*sahip oldukları sürece aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar. Alanların kullanılması, ve `area` `controller` `action`' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi oluşturur. Bu bölüm, yönlendirmenin alanlarla nasıl etkileşime gireceğini tartışır. alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](areas.md) bakın.

Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve adlı `Blog`bir alan için bir *alan yolu* kullanacak şekilde yapılandırır:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Benzer `/Manage/Users/AddUser`bir URL yolu eşleştirilirken, ilk yol yol değerlerini `{ area = Blog, controller = Users, action = AddUser }`oluşturur. `area` Yol değeri, tarafından `MapAreaRoute` oluşturulan yolun aşağıdaki değere eşit olduğu aslında için `area`varsayılan bir değer tarafından üretilir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`Bu durumda `area` `Blog`, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur. Varsayılan değer, yolun her zaman oluşturulmasını sağlar `{ area = Blog, ... }`, kısıtlama URL oluşturma için değeri `{ area = Blog, ... }` gerektirir.

> [!TIP]
> Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.

Yukarıdaki örneği kullanarak, yol değerleri aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

, `AreaAttribute` Bir alanın parçası olarak denetleyiciyi belirtir, bu denetleyicinin `Blog` alanında olduğunu varsayalım. Özniteliği olmayan denetleyiciler hiçbir alanın üyesi değildir ve `area` rota değeri yönlendirme tarafından sağlandığında **eşleşmeyecektir.** `[Area]` Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle `{ area = Blog, controller = Users, action = AddUser }`eşleştirebilir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Her denetleyicinin ad alanı, tamamlanma için burada gösterilir. Aksi takdirde, denetleyicilerde adlandırma çakışması olur ve derleyici hatası oluşturur. Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı `area` rota değeri tarafından sağlandığında eşleşir. Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer `area` sağlanmamışsa eşleşemez.

> [!NOTE]
> Değer *olmadan*eşleşme açısından `area` değerin yokluğu değeri null ya da boş dize olarak `area` aynıdır.

Bir alan içinde bir eylem yürütürken, için `area` rota DEĞERI, URL oluşturma için kullanılmak üzere yönlendirme için bir *ortam değeri* olarak kullanılabilir olacaktır. Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Iactionconstraint 'i anlama

> [!NOTE]
> Bu bölüm, Framework iç işlevleri hakkında ayrıntılı bir bakış ve MVC 'nin yürütülecek eylemi nasıl seçtiği. Tipik bir uygulamanın özel olması gerekmez`IActionConstraint`

Arabirime tanıdık olmasanız bile büyük `IActionConstraint` olasılıkla zaten kullandık. Özniteliği `[HttpGet]` ve benzer `[Http-VERB]` öznitelikleri, bir `IActionConstraint` eylem yönteminin yürütülmesini sınırlandırmak için uygular.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Varsayılan geleneksel yol varsayılıyor, URL yolu `/Products/Edit` burada gösterilen eylemlerin `{ controller = Products, action = Edit }` **her ikisiyle de** eşleşen değerleri üretir. `IActionConstraint` Terminolojisinde, her ikisi de rota verileriyle eşleşen her iki eylemin da aday kabul edileceğini söyliyoruz.

Yürütüldüğünde, *Düzenle ()* , Get için bir eşleşmedir ve diğer http fiili için bir eşleşme değildir. *GET* `HttpGetAttribute` `Edit(...)` Eylemin tanımlı hiçbir kısıtlaması yok, bu nedenle HERHANGI bir http fiili ile eşleşir. Bu nedenle, `POST` yalnızca `Edit(...)` bir eşleşme olduğunu varsayıyoruz. Ancak, `GET` her iki eylem de eşleşemez, ancak bir eylem `IActionConstraint` , olmadan bir eylemden her zaman *daha iyi* kabul edilebilir. Bu nedenle `Edit()` , daha belirli bir kabul edildiğinden ve her iki eylem de eşleşeceğinden seçilecek. `[HttpGet]`

Kavramsal olarak `IActionConstraint` , bir *aşırı yükleme*biçimidir, ancak aynı ada sahip yöntemlerin AŞıRı yüklenmesi yerine aynı URL ile eşleşen eylemler arasında aşırı yükleme yapılır. Öznitelik yönlendirme Ayrıca `IActionConstraint` , farklı denetleyicilerle her ikisi de olarak kabul edilen eylemlere neden olabilir.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Iactionconstraint uygulama

' `IActionConstraint` Nin uygulanmasının en kolay yolu, ' dan `System.Attribute` türetilmiş bir sınıf oluşturmaktır ve bunları eylemleriniz ve denetleyicilerinize yerleştirmelidir. MVC, öznitelik olarak uygulanan `IActionConstraint` her türlü otomatik olarak keşfedilir. Kısıtlama uygulamak için uygulama modelini kullanabilirsiniz ve bu, büyük olasılıkla en esnek yaklaşımdır ve bu sayede, nasıl uygulanabileceğini meta programlayabilirsiniz.

Aşağıdaki örnekte, bir kısıtlama yol verilerinden bir *ülke kodunu* temel alan bir eylem seçer. [GitHub 'daki tam örnek](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

`Accept` Yöntemi uygulamaktan ve kısıtlamanın yürütülmesi için bir ' Order ' seçmeye sorumlusunuz. Bu durumda, `Accept` yöntemi, `true` `country` yol değeri eşleştiğinde eylemin bir eşleşme olduğunu belirtmek için öğesini döndürür. Bu, öğesinden farklı bir `RouteValueAttribute` eyleme geri dönüş yapılmasına izin verir. Örnek, bir `en-US` eylem tanımlarsanız, gibi `fr-FR` bir ülke kodunun, `[CountrySpecific(...)]` uygulanmamış daha fazla genel denetleyiciye geri dönemeyeceğini gösterir.

`Order` Özelliği kısıtlamanın parçası olan *aşamayı* belirler. Eylem kısıtlamaları, `Order`gruplarına göre gruplar içinde çalışır. Örneğin, tüm Framework tarafından sunulan HTTP yöntemi öznitelikleri aynı aşamada çalışacak şekilde aynı `Order` değeri kullanır. İstediğiniz ilkeleri uygulamak için ihtiyacınız olan çok sayıda aşamaya sahip olabilirsiniz.

> [!TIP]
> Bir değere karar vermek için, `Order` KıSıTLAMALARıNıZıN http yöntemlerinden önce uygulanıp uygulanmayacağı hakkında düşünün. Daha az sayı önce çalışır.

::: moniker-end
