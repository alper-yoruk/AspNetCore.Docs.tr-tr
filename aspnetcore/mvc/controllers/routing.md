---
title: ASP.NET Core denetleyici eylemlerine yönlendirme
author: rick-anderson
description: ASP.NET Core MVC 'nin, gelen isteklerin URL 'Lerini eşleştirmek ve bunları eylemlerle eşlemek için yönlendirme ara yazılımını nasıl kullandığını öğrenin.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- appsettings.json
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
uid: mvc/controllers/routing
ms.openlocfilehash: 44c507fb5e0ff4477a84bfc1e4d0c62180c8dd37
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252844"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core denetleyici eylemlerine yönlendirme

[Ryan şimdi ak](https://github.com/rynowak), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core denetleyicileri, gelen isteklerin URL 'Leriyle eşleştirmek ve bunları [eylemlerle](#action)eşlemek için yönlendirme [Ara yazılımını](xref:fundamentals/middleware/index) kullanır.  Yol şablonları:

* , Başlangıç kodunda veya özniteliklerde tanımlanmıştır.
* URL yollarının [eylemlerle](#action)nasıl eşleştiğini betimleyen.
* Bağlantıların URL 'Leri oluşturmak için kullanılır. Oluşturulan bağlantılar genellikle yanıtlar halinde döndürülür.

Eylemler [genel olarak-yönlendirildi](#cr) veya [Attribute-yönlendirildi](#ar)' dir. Bir yolun denetleyiciye veya [eyleme](#action) yerleştirilmesi, BT özniteliğinin yönlendirilmesini sağlar. Daha fazla bilgi için bkz. [karma yönlendirme](#routing-mixed-ref-label) .

Bu belge:

* MVC ve yönlendirme arasındaki etkileşimleri açıklar:
  * Tipik MVC uygulamalarının yönlendirme özelliklerini kullanma şekli.
  * Her ikisini de içerir:
    * [Geleneksel yönlendirme](#cr) genellikle denetleyiciler ve görünümlerle kullanılır.
    * REST API 'Leri ile kullanılan *öznitelik yönlendirme* . Birincil olarak REST API 'Leri için yönlendirme ile ilgileniyorsanız, [REST API 'leri Için öznitelik yönlendirme](#ar) bölümüne atlayın.
  * Bkz. Gelişmiş yönlendirme ayrıntıları için [yönlendirme](xref:fundamentals/routing) .
* ASP.NET Core 3,0 ' de eklenen varsayılan yönlendirme sisteminin Endpoint Routing olarak adlandırıldığını gösterir. Uyumluluk amaçlarıyla önceki yönlendirme sürümü ile denetleyicileri kullanmak mümkündür. Yönergeler için [2.2-3.0 geçiş kılavuzuna](xref:migration/22-to-30) bakın. Eski yönlendirme sistemindeki başvuru malzemeleri için [Bu belgenin 2,2 sürümüne](xref:mvc/controllers/routing?view=aspnetcore-2.2) bakın.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Geleneksel rotayı ayarlama

`Startup.Configure` genellikle [geleneksel yönlendirme](#crd)kullanılırken aşağıdakine benzer bir kod içerir:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Çağrısı içinde <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> tek bir yol oluşturmak için kullanılır. Tek yol yol olarak adlandırılır `default` . Denetleyiciler ve görünümler içeren çoğu uygulama, yola benzer bir rota şablonu kullanır `default` . REST API 'Leri [öznitelik yönlendirmeyi](#ar)kullanmalıdır.

Yol şablonu `"{controller=Home}/{action=Index}/{id?}"` :

* Şöyle bir URL yoluyla eşleşir `/Products/Details/5`
* `{ controller = Products, action = Details, id = 5 }`Yolu simgeleştirileyerek yol değerlerini ayıklar. Uygulamanın adlı bir denetleyici ve bir eylem varsa yol değerlerinin ayıklanması bir eşleşme ile sonuçlanır `ProductsController` `Details` :

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5` model, `id = 5` parametresini olarak ayarlamak için değerini bağlar `id` `5` . Daha fazla ayrıntı için bkz. [model bağlama](xref:mvc/models/model-binding) .
* `{controller=Home}``Home`Varsayılan olarak tanımlar `controller` .
* `{action=Index}``Index`Varsayılan olarak tanımlar `action` .
*  `?`İçindeki karakter, `{id?}` `id` isteğe bağlı olarak tanımlar.
  * Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez. Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .
* URL yoluyla eşleşir `/` .
* Yol değerlerini üretir `{ controller = Home, action = Index }` .

İçin değerleri `controller` ve `action` varsayılan değerleri kullanır. `id` URL yolunda karşılık gelen bir kesim olmadığından değer üretmez. `/` yalnızca bir ve eylemi varsa eşleşir `HomeController` `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Önceki denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yolları için çalıştırılır:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL yolu, `/` yönlendirme şablonu varsayılan `Home` denetleyicilerini ve eylemini kullanır `Index` . URL yolu, `/Home` yönlendirme şablonu varsayılan eylemini kullanır `Index` .

Kolaylık yöntemi <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> :

```csharp
endpoints.MapDefaultControllerRoute();
```

Yerine

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Yönlendirme, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ara yazılımı kullanılarak yapılandırılır. Denetleyicileri kullanmak için:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` [Öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için içinde çağırın.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> Hem [genel olarak yönlendirmeli](#cr) denetleyicileri hem de [öznitelik yönlendirmeli](#ar) denetleyicileri eşlemek için veya öğesini çağırın.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Geleneksel yönlendirme

Geleneksel yönlendirme, denetleyiciler ve görünümlerle kullanılır. `default`Yol:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

, *geleneksel yönlendirmeye* bir örnektir. Bu, URL yolları için bir *kural* oluşturduğundan *geleneksel yönlendirme* olarak adlandırılır:

* İlk yol segmenti, `{controller=Home}` , denetleyici adıyla eşlenir.
* İkinci kesim, `{action=Index}` , [eylem](#action) adıyla eşlenir.
* Üçüncü segment, `{id?}` isteğe bağlı olarak kullanılır `id` . `?`İçinde, `{id?}` isteğe bağlı yapar. `id` bir model varlığına eşlemek için kullanılır.

Bu `default` yolu kullanarak, URL yolu:

* `/Products/List``ProductsController.List`eyleme eşlenir.
* `/Blog/Article/17` ile eşlenir `BlogController.Article` ve model genellikle `id` parametresini 17 ' ye bağlar.

Bu eşleme:

* **Yalnızca** denetleyiciyi ve [eylem](#action) adlarını temel alır.
* Ad alanlarını, kaynak dosya konumlarını veya yöntem parametrelerini temel alan değildir.

Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, her eylem için yeni bir URL düzeniyle karşılaşmadan uygulamanın oluşturulmasına olanak sağlar. [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) stilinde eylemlere sahip bir uygulama için, denetleyiciler arasında URL 'ler için tutarlılık vardır:

* Kodu basitleştirmeye yardımcı olur.
* Kullanıcı arabirimini daha öngörülebilir hale getirir.

> [!WARNING]
> `id`Önceki kodda, yol şablonu tarafından isteğe bağlı olarak tanımlanmıştır. Eylemler, URL 'nin bir parçası olarak belirtilen isteğe bağlı KIMLIK olmadan çalıştırılabilir. Genellikle, `id` URL 'den atlandığında:
>
> * `id` , `0` model bağlama tarafından olarak ayarlanır.
> * Veritabanında eşleşen bir varlık bulunamadı `id == 0` .
>
> [Öznitelik yönlendirme](#ar) , kimliği bazı eylemler için gerekli hale getirmek için ayrıntılı denetim sağlar ve diğerleri için değildir. Kurala göre belgeler, `id` doğru kullanımlarda görünebilecekleri gibi isteğe bağlı parametreler içerir.

Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}` :

* Temel ve açıklayıcı bir yönlendirme düzenini destekler.
* , UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.
* Birçok Web UI uygulaması için tek yol şablonu gereklidir. Daha büyük Web UI uygulamaları için, [alan](#areas) kullanan başka bir yol da sıkça gereklidir.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* , Çağrıldığı sıraya göre kendi uç noktalarına otomatik olarak bir **sipariş** değeri atayın.

ASP.NET Core 3,0 ve üzeri için uç nokta yönlendirme:

* Bir yol kavramı yoktur.
* , Genişletilebilirlik yürütülmesi için sıralama garantisi sağlamaz, tüm uç noktalar bir kerede işlenir.

İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının nasıl yapıldığını görmek için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin <xref:Microsoft.AspNetCore.Routing.Route> .

[Öznitelik yönlendirme](#ar) bu belgenin ilerleyen kısımlarında açıklanmıştır.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Birden çok geleneksel yollar

[](#cr) `UseEndpoints` Ve ' ye daha fazla çağrı eklenerek, içine birden çok geleneksel yol eklenebilir <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . Bunun yapılması, birden çok kural tanımlamayı veya belirli bir [eyleme](#action)adanmış geleneksel yollar eklemeyi sağlar; örneğin:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

`blog`Önceki koddaki yol, **ayrılmış bir geleneksel yoldur**. Ayrılmış bir geleneksel yol olarak adlandırılır çünkü:

* [Geleneksel yönlendirme](#cr)kullanır.
* Belirli bir [eyleme](#action)ayrılmıştır.

`controller`Ve `action` yol şablonunda parametre olarak görünmediği için `"blog/{*article}"` :

* Yalnızca varsayılan değerleri olabilir `{ controller = "Blog", action = "Article" }` .
* Bu yol her zaman eyleme eşlenir `BlogController.Article` .

`/Blog`, `/Blog/Article` ve, `/Blog/{any-string}` Blog rotası ile eşleşen tek URL yollarıdır.

Önceki örnek:

* `blog` yol, önce eklendiğinden, rotadan eşleşme için daha yüksek önceliğe sahiptir `default` .
* , URL 'nin bir parçası olarak bir makale adının olması gereken tipik bir [başlık](https://developer.mozilla.org/docs/Glossary/Slug) stili yönlendirme örneğidir.

> [!WARNING]
> ASP.NET Core 3,0 ve üzeri sürümlerde yönlendirme:
> * *Yol* adlı bir kavram tanımlayın. `UseRouting` ara yazılım ardışık düzenine eşleşen rota ekler. `UseRouting`Ara yazılım, uygulamada tanımlanan uç noktalar kümesine bakar ve isteğe bağlı olarak en iyi uç nokta eşleşmesini seçer.
> * Veya gibi genişletilebilirlik yürütme sırası hakkında garanti sağlar <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .
>
>Bkz. yönlendirme ile ilgili başvuru malzemeleri için [yönlendirme](xref:fundamentals/routing) .

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Geleneksel yönlendirme sırası

Geleneksel yönlendirme yalnızca uygulama tarafından tanımlanan eylem ve denetleyicinin bir bileşimiyle eşleşir. Bu, geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.
,, Ve kullanarak yollar ekleme, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> çağırdıkları sıraya göre bitiş noktalarına otomatik olarak bir sipariş değeri atar. Daha önce görüntülenen bir rotadaki eşleşmelerin önceliği daha yüksektir. Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir. Catch-all yol parametrelerine sahip [adanmış geleneksel yollar](#dcr) `{*article}` , bir yol çok uzun sürebilir [](xref:fundamentals/routing#greedy), yani diğer yollarla eşleştirmek istediğiniz URL 'lerle eşleşir. Doyumsuz yollarını daha sonra yol tablosuna yerleştirerek doyumsuz eşleşmelerini önleyin.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Belirsiz eylemleri çözme

Yönlendirme ile iki uç nokta eşleşmesi durumunda, yönlendirme aşağıdakilerden birini yapmanız gerekir:

* En iyi aday ' ı seçin.
* Bir özel durum oluşturur.

Örneğin:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Yukarıdaki denetleyici, eşleşen iki eylemi tanımlar:

* URL yolu `/Products33/Edit/17`
* Veri yönlendirin `{ controller = Products33, action = Edit, id = 17 }` .

Bu, MVC denetleyicileri için tipik bir modeldir:

* `Edit(int)` bir ürünü düzenlemek için bir form görüntüler.
* `Edit(int, Product)` Postalanan formu işler.

Doğru yolu çözümlemek için:

* `Edit(int, Product)` istek bir HTTP olduğunda seçilir `POST` .
* `Edit(int)`[http fiili](#verb) başka bir şey olduğunda seçilir. `Edit(int)` genellikle aracılığıyla çağrılır `GET` .

, <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` İsteğin HTTP yöntemine göre seçim yapabilmesi için yönlendirme için verilmiştir. , `HttpPostAttribute` `Edit(int, Product)` Daha iyi bir eşleşme yapar `Edit(int)` .

Gibi özniteliklerin rolünü anlamanız önemlidir `HttpPostAttribute` . Benzer öznitelikler diğer [http fiilleri](#verb)için tanımlanmıştır. [Geleneksel yönlendirmesinde](#cr), bir görüntüleme formu, gönder formu iş akışının bir parçası olduklarında aynı eylem adını kullanmak yaygın olarak kullanılan bir işlemdir. Örneğin, bkz. [Iki düzenleme eylemi yöntemini İnceleme](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Yönlendirme bir en iyi aday seçebilirse, <xref:System.Reflection.AmbiguousMatchException> birden fazla eşleşen uç noktayı listeleyerek bir oluşturulur.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Geleneksel yol adları

Dizeler  `"blog"` ve `"default"` Aşağıdaki örneklerde geleneksel yol adları verilmiştir:

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

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

Önceki kodda, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> `UseEndpoints` öznitelik yönlendirmeli denetleyicileri eşlemek için içinde çağırılır.

Aşağıdaki örnekte:

* Önceki `Configure` yöntem kullanılır.
* `HomeController` Varsayılan geleneksel yolun eşleşdiküyle benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Eylem,, `HomeController.Index` veya URL yollarından herhangi biri için çalıştırılır `/` `/Home` `/Home/Index` `/Home/Index/3` .

Bu örnek, öznitelik yönlendirme ve [geleneksel yönlendirme](#cr)arasında bir temel programlama farkı vurgulamaktadır. Öznitelik yönlendirme için bir yol belirtmek için daha fazla giriş gerekir. Geleneksel varsayılan yol, yönlendirmeleri daha succinctly işler. Ancak, öznitelik yönlendirme izin verir ve her [eyleme](#action)hangi rota şablonlarının uygulanacağını kesin olarak denetler.

Öznitelik yönlendirme ile, denetleyici ve eylem adları, [belirteç değiştirme](#routing-token-replacement-templates-ref-label) kullanılmadığı takdirde, eylemin eşleştirildiği hiçbir parçasını oynar. Aşağıdaki örnek, önceki örnekle aynı URL 'Lerle eşleşir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Aşağıdaki kod, ve için belirteç yerini `action` kullanır `controller` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Aşağıdaki kod `[Route("[controller]/[action]")]` Denetleyici için geçerlidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Yukarıdaki kodda, `Index` Yöntem şablonlarının sonuna `/` veya yol şablonlarına eklenmiş olması gerekir `~/` . Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları.

Rota şablonu seçimi hakkında bilgi için bkz. [route Template önceliği](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Ayrılmış yönlendirme adları

Aşağıdaki anahtar sözcükler, denetleyiciler veya sayfalar kullanılırken ayrılmış yol parametresi adlarıdır Razor :

* `action`
* `area`
* `controller`
* `handler`
* `page`

`page`Öznitelik yönlendirme ile yol parametresi olarak kullanmak yaygın bir hatadır. Bunun yapılması, URL oluşturma ile tutarsız ve kafa karıştırıcı davranışa neden olur.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Özel parametre adları URL oluşturma tarafından bir URL oluşturma işleminin bir sayfaya mı yoksa bir denetleyiciye mi başvurduğunu belirlemekte kullanılır Razor .

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

* Her eylem `[HttpGet]` yalnızca http get istekleriyle eşleştirmeyi kısıtlayan özniteliği içerir.
* `GetProduct`Eylem, şablonu içerir `"{id}"` , bu nedenle `id` `"api/[controller]"` denetleyicideki şablonun sonuna eklenir. Yöntemler şablonu `"api/[controller]/"{id}""` . Bu nedenle bu eylem yalnızca `/api/test2/xyz` ,, `/api/test2/123` vb. için get istekleri ile eşleşir `/api/test2/{any string}` .
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct`Eylem `"int/{id:int}")` şablonu içerir. `:int`Şablonun kısmı, `id` yol değerlerini bir tamsayıya dönüştürülebileceği dizelere kısıtlar. Bir GET isteği `/api/test2/int/abc` :
  * Bu eylemle eşleşmiyor.
  * Bir [404 bulunamadı](https://developer.mozilla.org/docs/Web/HTTP/Status/404) hatası döndürür.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product`Eylem şablonda bulunur `{id}` , ancak `id` bir tamsayıya dönüştürülemeyen değerlerle sınırlandırmaz. Bir GET isteği `/api/test2/int2/abc` :
  * Bu rota ile eşleşir.
  * Model bağlama `abc` bir tamsayıya dönüştürülemez. `id`Yönteminin parametresi tamsayıdır.
  * Model bağlama bir tamsayıya dönüştürülemediğinden, [400 hatalı bir istek](https://developer.mozilla.org/docs/Web/HTTP/Status/400) döndürür `abc` .
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Öznitelik yönlendirme,, <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> ve gibi öznitelikleri <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> kullanabilir <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . Tüm [http fiili](#verb) öznitelikleri bir yol şablonunu kabul eder. Aşağıdaki örnekte, aynı yol şablonuyla eşleşen iki eylem gösterilmektedir:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL yolu kullanılarak `/products3` :

* `MyProductsController.ListProducts`Eylem [http fiili](#verb) olduğunda çalışır `GET` .
* `MyProductsController.CreateProduct`Eylem [http fiili](#verb) olduğunda çalışır `POST` .

Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul ettiğinden bir eylem yönteminde kullanmanız gerekecektir. API 'nizin neleri desteklediği hakkında kesin olması için, daha özel [http fiili özniteliği](#verb) kullanmak daha iyidir. REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.

REST API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır. Bu, örneğin, aynı mantıksal kaynaktaki al ve postala gibi birçok işlemin aynı URL 'YI kullanması anlamına gelir. Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Aşağıdaki örnekte, `id` URL yolunun bir parçası olarak gereklidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)`Eylem:

* , Şunun gibi URL yoluyla çalıştırılır `/products2/3`
* URL yoluyla çalıştırılmadı `/products2` .

[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar. Daha fazla bilgi için bkz. [desteklenen istek içerik türlerini tüketir özniteliğiyle tanımlama](xref:web-api/index#consumes).

 Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .

Hakkında daha fazla bilgi için `[ApiController]` bkz. [Apicontroller özniteliği](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Yönlendirme adı

Aşağıdaki kod, bir yol adı tanımlar `Products_List` :

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir. Yol adları:

* Yönlendirmenin URL eşleşen davranışı üzerinde hiçbir etkisi yoktur.
* Yalnızca URL oluşturma için kullanılır.

Yol adları, uygulama genelinde benzersiz olmalıdır.

Önceki kodu, `id` parametreyi isteğe bağlı () olarak tanımlayan geleneksel varsayılan rotayla kontrast `{id?}` . API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Öznitelik yollarını birleştirme

Öznitelik yönlendirmeyi daha az tekrarlı hale getirmek için, denetleyicideki yol öznitelikleri, bireysel eylemlerdeki rota öznitelikleriyle birleştirilir. Denetleyicide tanımlanan tüm yol şablonları, eylemlerdeki rota şablonlarına eklenir. Bir Route özniteliğinin denetleyiciye yerleştirilmesi, denetleyicideki **Tüm** eylemlerin öznitelik yönlendirme kullanmasını sağlar.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Yukarıdaki örnekte:

* URL yolu `/products` eşleşiyor `ProductsApi.ListProducts`
* URL yolu `/products/5` eşleşiyor `ProductsApi.GetProduct(int)` .

Bu eylemlerin her ikisi de, özniteliğiyle işaretlendiğinden yalnızca HTTP ile eşleşir `GET` `[HttpGet]` .

Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları. Aşağıdaki örnek, varsayılan rotaya benzer bir URL yolları kümesiyle eşleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

Aşağıdaki tabloda `[Route]` önceki koddaki öznitelikler açıklanmaktadır:

| Öznitelik               | İle birleştirir `[Route("Home")]` | Rota şablonunu tanımlar |
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

Örneğin, gibi bir öznitelik yolu, `blog/search/{topic}` gibi bir öznitelik rotasına göre daha özgüdür `blog/{*article}` . `blog/search/{topic}`Daha belirgin olduğundan, yolun önceliği daha yüksektir. [Geleneksel yönlendirmeyi](#cr)kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.

Öznitelik yolları özelliği kullanarak bir sıra yapılandırabilir <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> . Tüm Framework 'ün [yol öznitelikleri](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dahil edilmiştir `Order` . Yollar, özelliğin artan sıralamasına göre işlenir `Order` . Varsayılan sıra `0` . Bir `Order = -1` siparişi ayarlamadığı rotalardan önce çalıştırmaları kullanarak bir rota ayarlama. `Order = 1`Varsayılan yol sıralaması sonrasında çalıştırmaları kullanarak bir rota ayarlama.

 Uygulamasına bağlı kaçının `Order` . Bir uygulamanın URL 'SI alanı, doğru şekilde yönlendirmek için açık sıra değerleri gerektiriyorsa, bu durumda istemciler de kafa karıştırıcı olabilir. Genel olarak, öznitelik yönlendirme URL ile eşleşen doğru yolu seçer. URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, geçersiz kılma olarak bir yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .

Her ikisi de eşleşen yolu tanımlayan aşağıdaki iki denetleyicisi göz önünde bulundurun `/home` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

`/home`Yukarıdaki kodla istemek şuna benzer bir özel durum oluşturur:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

`Order`Yol özniteliklerinden birine ekleme belirsizliği çözer:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Yukarıdaki kodla `/home` `HomeController.Index` bitiş noktasını çalıştırır. Öğesine ulaşmak için `MyDemoController.MyIndex` `/home/MyIndex` . **Note**:

* Yukarıdaki kod bir örnek veya kötü yönlendirme tasarımdır. Özelliği göstermek için kullanılmıştı `Order` .
* `Order`Özelliği yalnızca belirsizlik çözümleniyor, bu şablon eşleştirilemez. Şablonu kaldırmak daha iyi olacaktır `[Route("Home")]` .

Bkz. [ Razor sayfa yönlendirme ve uygulama kuralları:](xref:razor-pages/razor-pages-conventions#route-order) sayfalarla rota sıralaması hakkında bilgi için rota sırası Razor .

Bazı durumlarda, belirsiz yollarla bir HTTP 500 hatası döndürülür. Hangi uç noktaların neden olduğunu görmek için [günlük kaydını](xref:fundamentals/logging/index) kullanın `AmbiguousMatchException` .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Yönlendirme şablonlarında belirteç değiştirme [denetleyici], [eylem], [alan]

Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (,) içine alarak *belirteç değiştirmeyi* destekler `[` `]` . , Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Yukarıdaki kodda:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Eşleştir `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Eşleştir `/Products0/Edit/{id}`

Belirteç değişikliği, öznitelik yollarının oluşturulması için son adım olarak gerçekleşir. Yukarıdaki örnek aşağıdaki kodla aynı şekilde davranır:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Öznitelik rotaları de devralma ile birleştirilebilir. Bu, belirteç değiştirme ile güçlü bir şekilde birleştirilir. Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`Her eylem için benzersiz bir yol adı üretir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
Her eylem için benzersiz bir yol adı üretir.

Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya  `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın

Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> , parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini şu şekilde değiştirir `subscription-management` :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Şu şekilde bir uygulama modeli kuralıdır:

* Bir uygulamadaki tüm öznitelik yollarına bir parametre transformatörü uygular.
* Öznitelik yol belirteci değerlerini değiştirildikleri gibi özelleştirir.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Önceki `ListAll` Yöntem eşleşir `/subscription-management/list-all` .

, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Bilgi tanımı için bkz. [Mdıdn Web docs](https://developer.mozilla.org/docs/Glossary/Slug) for the başlık.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Birden çok öznitelik yolu

Öznitelik yönlendirme, aynı eyleme ulaşan birden çok yolun tanımlanmasını destekler. Bunun en yaygın kullanımları, aşağıdaki örnekte gösterildiği gibi varsayılan geleneksel yolun davranışını taklit etmek olur:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Denetleyiciye birden çok yol özniteliği koymak, her birinin eylem yöntemlerinde yol özniteliklerinin her biri ile birleştiribileceği anlamına gelir:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Tüm [http fiili](#verb) yol kısıtlamaları uygulanır `IActionConstraint` .

Uygulayan birden çok yol özniteliği <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> bir eyleme yerleştirildiğinde:

* Her eylem kısıtlaması, denetleyiciye uygulanan rota şablonuyla birlikte birleşir.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Eylemlerde birden çok yolun kullanılması yararlı ve güçlü görünebilir, uygulamanızın URL alanını temel ve iyi tanımlanmış tutmanız daha iyidir. **Yalnızca** gerektiğinde, var olan istemcileri desteklemek için, eylemlerde birden çok yol kullanın.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Öznitelik rotası isteğe bağlı parametreler, varsayılan değerler ve kısıtlamalar belirtme

Öznitelik yolları, isteğe bağlı parametreleri, varsayılan değerleri ve kısıtlamaları belirtmek için geleneksel yollarla aynı satır içi sözdizimini destekler.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Yukarıdaki kodda, `[HttpPost("product14/{id:int}")]` bir yol kısıtlaması uygular. `Products14Controller.ShowProduct`Eylem yalnızca, gıbı URL yollarıyla eşleştirilir `/product14/3` . Yol şablonu bölümü, `{id:int}` Bu segmenti yalnızca tamsayılarla kısıtlar.

Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Iroutetemplateprovider kullanarak özel yol öznitelikleri

Tüm [Rota öznitelikleri](#rt) uygular <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> . ASP.NET Core çalışma zamanı:

* Uygulama başladığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar.
* `IRouteTemplateProvider`İlk yol kümesini oluşturmak için uygulayan öznitelikleri kullanır.

`IRouteTemplateProvider`Özel yol özniteliklerini tanımlamak için uygulayın. Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Önceki `Get` Yöntem döndürülür `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Öznitelik yollarını özelleştirmek için uygulama modelini kullanın

Uygulama modeli:

* , Başlangıçta oluşturulan bir nesne modelidir.
* Bir uygulamadaki eylemleri yönlendirmek ve yürütmek için ASP.NET Core tarafından kullanılan tüm meta verileri içerir.

Uygulama modeli, yol özniteliklerinden toplanan tüm verileri içerir. Yol özniteliklerinin verileri uygulama tarafından sağlanır `IRouteTemplateProvider` . Adlandır

* , Yönlendirmenin nasıl davranacağını özelleştirmek için uygulama modelini değiştirmek üzere yazılabilir.
* Uygulama başlangıcında okundu.

Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin temel bir örneği gösterilmektedir. Aşağıdaki kod, rotaları projenin klasör yapısıyla kabaca bir şekilde oluşturur.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Aşağıdaki kod, `namespace` kuralı yönlendirilen denetleyicilere uygulanmasını engeller:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Örneğin, aşağıdaki denetleyici kullanmaz `NamespaceRoutingConvention` :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply`Yöntemi:

* Denetleyici öznitelik yönlendirmemişse hiçbir şey yapmaz.
* Temel kaldırılmış olarak, denetleyici şablonunu öğesine göre ayarlar `namespace` `namespace` .

, `NamespaceRoutingConvention` ' De uygulanabilir `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Örneğin, aşağıdaki denetleyiciyi göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Yukarıdaki kodda:

* Temel `namespace` `My.Application` .
* Önceki denetleyicinin tam adı `My.Application.Admin.Controllers.UsersController` .
* , `NamespaceRoutingConvention` Denetleyiciler şablonunu olarak ayarlar `Admin/Controllers/Users/[action]/{id?` .

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

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki altyapının temel öğesidir. Bir örneği, `IUrlHelper` `Url` denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir.

Aşağıdaki örnekte, `IUrlHelper` arabirimi `Controller.Url` başka bir eyleme bir URL oluşturmak için özelliği aracılığıyla kullanılır.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkeninin DEĞERI URL yol dizesidir `/UrlGeneration/Destination` . Bu URL yolu, yönlendirme tarafından birleştirerek oluşturulur:

* Geçerli istekten, **ortam değerleri** olarak adlandırılan rota değerleri.
* `Url.Action`Bu değerleri yol şablonuna geçirilen ve yerine geçen değerler:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir. Bir değere sahip olmayan bir rota parametresi şunları yapabilir:

* Bir varsayılan değer varsa, bu değeri kullanın.
* İsteğe bağlı ise atlanır. Örneğin, `id` Rota şablonundan `{controller}/{action}/{id?}` .

Herhangi bir gerekli yol parametresi karşılık gelen bir değere sahip değilse, URL oluşturma başarısız olur. Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.

Yukarıdaki örnekte `Url.Action` [geleneksel yönlendirme](#cr)varsayılır. URL oluşturma, [öznitelik yönlendirme](#ar)ile benzer şekilde çalışır, ancak kavramlar farklıdır. Geleneksel yönlendirme ile:

* Rota değerleri bir şablonu genişletmek için kullanılır.
* İçin yol değerleri `controller` ve `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme ile eşleşen URL 'Ler bir kurala bağlı olduğundan, bu işe yarar.

Aşağıdaki örnek öznitelik yönlendirme kullanır:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

`Source`Yukarıdaki koddaki eylem oluşturulur `custom/url/to/destination` .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> ASP.NET Core 3,0 ' ye alternatif olarak eklenmiştir `IUrlHelper` . `LinkGenerator` benzer ancak daha esnek işlevler sunar. Üzerindeki her `IUrlHelper` bir yöntemi, buna karşılık gelen bir yöntem ailesini `LinkGenerator` de içerir.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL 'Leri oluşturma

[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirtilerek hedef uç noktası oluşturmak için tasarlanmıştır.

Kullanırken `Url.Action` , ve için geçerli yol değerleri `controller` `action` çalışma zamanı tarafından sağlanır:

* `controller`Ve değeri `action` hem [ortam değerlerinin](#ambient) hem de değerlerinin bir parçasıdır. Yöntemi `Url.Action` her zaman ve geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturur.

Yönlendirme, bir URL oluştururken sağlanmayan bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmaya çalışır. Ortam değerleriyle benzer bir yol düşünün `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` :

* Yönlendirmenin ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır.
* Tüm rota parametrelerinin bir değeri olduğundan, yönlendirmeye yeterli bilgi vardır.

Değer `{ d = Donovan }` eklenirse:

* Değer `{ d = David }` yok sayılır.
* Oluşturulan URL yolu `Alice/Bob/Carol/Donovan` .

**Uyarı**: URL yolları hiyerarşiktir. Yukarıdaki örnekte, değer `{ c = Cheryl }` eklenirse:

* Her iki değer de `{ c = Carol, d = David }` yok sayılır.
* Artık bir değer yoktur `d` ve URL oluşturma başarısız olur.
* İçin istenen değerleri `c` ve `d` bir URL oluşturmak için belirtilmelidir.  

Varsayılan yol ile bu soruna isabet etmeniz gerekebilir `{controller}/{action}/{id?}` . `Url.Action`Her zaman açıkça bir ve değeri belirttiğinden bu sorun pratikte nadir olarak belirlenir `controller` `action` .

URL 'nin çeşitli aşırı yüklemeleri [. eylem](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) , ve dışındaki rota parametreleri için değerler sağlamak üzere bir yol değerleri nesnesi alır `controller` `action` . Route Values nesnesi genellikle ile birlikte kullanılır `id` . Örneğin, `Url.Action("Buy", "Products", new { id = 17 })`. Yol değerleri nesnesi:

* Kural gereği genellikle anonim türdeki bir nesnedir.
* Bir `IDictionary<>` veya [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)olabilir).

Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Yukarıdaki kod oluşturulur `/Products/Buy/17?color=red` .

Aşağıdaki kod mutlak URL oluşturur:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Mutlak URL oluşturmak için, aşağıdakilerden birini kullanın:

* Kabul eden bir aşırı yükleme `protocol` . Örneğin, yukarıdaki kod.
* Varsayılan olarak mutlak URI 'Ler üreten [Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*).

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Yola göre URL oluşturma

Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir. `IUrlHelper` Ayrıca [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) ailesi yöntemlerin de sağlar. Bu yöntemler [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)ile benzerdir, ancak geçerli değerlerini `action` ve `controller` Rota değerlerine kopyalamaz. En yaygın kullanımı `Url.RouteUrl` :

* URL oluşturmak için bir yol adı belirtir.
* Genellikle bir denetleyici veya eylem adı belirtmez.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Aşağıdaki Razor Dosya, için BIR HTML bağlantısı oluşturur `Destination_Route` :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>HTML ve, URL 'Leri oluşturma Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>sırasıyla ve öğeleri oluşturmak Için [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) ve [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) yöntemlerini sağlar `<form>` `<a>` . Bu yöntemler bir URL oluşturmak için [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler. `Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.

Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur. Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır. Daha fazla bilgi için bkz. [Formlardaki etiket yardımcıları](xref:mvc/views/working-with-forms) .

Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Eylem sonuçlarında URL oluşturma

Yukarıdaki örneklerde `IUrlHelper` bir denetleyicide kullanılması gösterildi. Bir denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak bir URL oluşturmak olur.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase>Ve <xref:Microsoft.AspNetCore.Mvc.Controller> temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar. Kullanıcı girişini kabul ettikten sonra, yaygın olarak kullanılan bir kullanım yeniden yönlendirilmelidir:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Eylem, gibi Fabrika yöntemleri sonuçları <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Adanmış geleneksel yollar için özel durum

[Geleneksel yönlendirme](#cr) , [adanmış geleneksel yol](#dcr)olarak adlandırılan özel bir yol tanımı türünü kullanabilir. Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Önceki yol tanımlarını kullanarak, `Url.Action("Index", "Home")` yolu kullanarak URL yolunu oluşturur `/` `default` , ancak neden? Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .

[Adanmış geleneksel yollar](#dcr) , karşılık gelen bir rota parametresi olmayan varsayılan değerlerin özel bir davranışına dayanır ve bu da yol, URL oluşturma ile çok fazla [dodan](xref:fundamentals/routing#greedy) yararlanın. Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez. Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir. `blog`Değerler EŞLEŞMEDIĞINDEN URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` . Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Bölgeler](xref:mvc/controllers/areas) , ilgili işlevselliği ayrı olarak bir grup içinde düzenlemek için kullanılan bir MVC özelliğidir:

* Denetleyici eylemleri için yönlendirme ad alanı.
* Görünümler için klasör yapısı.

Alanların kullanılması, farklı alanlara sahip oldukları sürece bir uygulamanın aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar. Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` . Bu bölümde yönlendirmenin alanlarla nasıl etkileşim kurduğu açıklanmaktadır. Alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](xref:mvc/controllers/areas) bakın.

Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve bir adlandırılmış yolu kullanacak şekilde yapılandırır `area` `area` `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Önceki kodda, öğesini <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> oluşturmak için çağırılır `"blog_route"` . İkinci parametresi, `"Blog"` , alan adıdır.

Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` yol, `"blog_route"` yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` . `area`Rota değeri, için varsayılan bir değer tarafından üretilir `area` . Tarafından oluşturulan yol `MapAreaControllerRoute` , aşağıdaki ile eşdeğerdir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` . Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.

Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar daha önce bir alan olmadan rotalardan daha belirgin olduklarından yerleştirilmelidir.

Önceki örneği kullanarak, yol değerleri `{ area = Blog, controller = Users, action = AddUser }` aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) özniteliği, bir alanın parçası olarak denetleyiciyi belirtir. Bu denetleyici `Blog` alanında bulunur. Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir ve  `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmez. Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Her denetleyicinin ad alanı, tamamlanma açısından burada gösterilmiştir. Yukarıdaki denetleyiciler aynı ad alanını kullanıyorsa, bir derleyici hatası oluşturulur. Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` . Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .

<a name="aa"></a>

Değer *olmadan* eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.

Bir alan içinde bir eylem yürütürken, için rota değeri, `area` yönlendirme için, URL oluşturma için kullanılacak bir [çevresel değer](#ambient) olarak kullanılabilir. Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Aşağıdaki kod, için bir URL oluşturur `/Zebra/Users/AddUser` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Eylem tanımı

Bir denetleyicide, [Nonactıon](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) özniteliğine sahip olanlar hariç genel yöntemler eylemlerdir.

## <a name="sample-code"></a>Örnek kod

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
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

Çağrısı içinde `UseMvc` , `MapRoute` yolu olarak başvurabileceğiniz tek bir yol oluşturmak için kullanılır `default` . Çoğu MVC uygulaması, yola benzer bir şablon içeren bir yol kullanır `default` .

Yol şablonu, `"{controller=Home}/{action=Index}/{id?}"` gibi BIR URL yoluyla eşleşir `/Products/Details/5` ve `{ controller = Products, action = Details, id = 5 }` yolu simgeleştirileyerek yol değerlerini ayıklar. MVC adlı bir denetleyiciyi bulmayı dener ve bu `ProductsController` eylemi çalıştırır `Details` :

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Bu örnekte, model bağlamanın `id = 5` `id` Bu eylemi çağırırken parametresini olarak ayarlamak için değerini kullanacağı unutulmamalıdır `5` . Daha fazla ayrıntı için [model bağlamaya](../models/model-binding.md) bakın.

Yolu kullanarak `default` :

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Yol şablonu:

* `{controller=Home}``Home`Varsayılan olarak tanımlar`controller`

* `{action=Index}``Index`Varsayılan olarak tanımlar`action`

* `{id?}``id`isteğe bağlı olarak tanımlar

Bir eşleşme için URL yolunda varsayılan ve isteğe bağlı yol parametrelerinin mevcut olması gerekmez. Yol şablonu sözdiziminin ayrıntılı açıklaması için bkz. [route Template Reference](xref:fundamentals/routing#route-template-reference) .

`"{controller=Home}/{action=Index}/{id?}"` URL yoluyla eşleştirebilir `/` ve yol değerlerini oluşturacaktır `{ controller = Home, action = Index }` . , `controller` `action` `id` URL yolunda karşılık gelen bir kesim olmadığından, için değerleri ve varsayılan değerleri kullanır, bir değer üretmez. MVC, ve eylemini seçmek için bu yol değerlerini `HomeController` kullanır `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Bu denetleyici tanımı ve yönlendirme şablonunu kullanarak, `HomeController.Index` eylem AŞAĞıDAKI URL yollarından herhangi biri için yürütülür:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Kolaylık yöntemi `UseMvcWithDefaultRoute` :

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

`UseMvc` ve `UseMvcWithDefaultRoute` `RouterMiddleware` , ara yazılım ardışık düzenine bir örneği ekleyin. MVC, doğrudan ara yazılım ile etkileşime girmez ve istekleri işlemek için yönlendirmeyi kullanır. MVC, bir örneği aracılığıyla yollara bağlanır `MvcRouteHandler` . İçindeki kod `UseMvc` Şuna benzer:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` hiçbir yol doğrudan tanımlamaz, yol için yol koleksiyonuna bir yer tutucu ekler `attribute` . Aşırı yükleme `UseMvc(Action<IRouteBuilder>)` kendi rotalarınızı eklemenize olanak tanır ve öznitelik yönlendirmeyi de destekler.  `UseMvc` ve tüm varyasyonları, yapılandırma şeklinden bağımsız olarak her zaman için öznitelik rotası özniteliği yönlendirme özelliği için bir yer tutucu ekler `UseMvc` . `UseMvcWithDefaultRoute` Varsayılan bir yol tanımlar ve öznitelik yönlendirmeyi destekler. [Öznitelik yönlendirme](#attribute-routing-ref-label) bölümü öznitelik yönlendirme hakkında daha fazla ayrıntı içerir.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Geleneksel yönlendirme

`default`Yol:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Yukarıdaki kod geleneksel yönlendirmeye bir örnektir. Bu stil, URL yolları için bir *kural* oluşturduğundan geleneksel yönlendirme olarak adlandırılır:

* İlk yol segmenti, denetleyicinin adıyla eşlenir.
* İkincisi eylem adıyla eşlenir.
* Üçüncü segment, isteğe bağlı olarak kullanılır `id` . `id` bir model varlığına eşlenir.

Bu `default` yolu kullanarak, URL yolu `/Products/List` eyleme eşlenir `ProductsController.List` ve `/Blog/Article/17` ile eşlenir `BlogController.Article` . Bu eşleme **yalnızca** denetleyiciye ve eylem adlarına dayalıdır ve ad alanları, kaynak dosya konumları veya yöntem parametrelerine göre değildir.

> [!TIP]
> Varsayılan yol ile geleneksel yönlendirmeyi kullanmak, tanımladığınız her eylem için yeni bir URL düzeniyle karşılaşmanıza gerek kalmadan uygulamayı hızlı bir şekilde oluşturmanıza olanak tanır. CRUD stilinde eylemlere sahip bir uygulama için denetleyicilerinizdeki URL 'Lerin tutarlılığı, kodunuzun basitleştirilmesine ve Kullanıcı arabiriminizi daha öngörülebilir hale getirmenize yardımcı olabilir.

> [!WARNING]
> , `id` Yol şablonu tarafından isteğe bağlı olarak tanımlanır ve bu, eylemlerinizin URL 'nin bir parçası olarak sağlanmadan yürütebileceği anlamına gelir. Genellikle `id` URL 'den atlanırsa ne olacağı, `0` model bağlama tarafından ayarlanabileceği ve sonuç olarak veritabanı eşleştirmesinin hiçbir varlık bulunamadığı durumlarda gerçekleşir `id == 0` . Öznitelik yönlendirme, bazı eylemler için gereken KIMLIĞI, diğerleri için değil, daha ayrıntılı bir denetim sağlayabilir. Kurala göre belgeler, `id` doğru kullanımda görünebilecekleri gibi isteğe bağlı parametreleri de içerecektir.

## <a name="multiple-routes"></a>Birden çok yol

`UseMvc`Uygulamasına daha fazla çağrı ekleyerek içine birden çok yol ekleyebilirsiniz `MapRoute` . Bunun yapılması, birden çok kural tanımlamanızı veya belirli bir eyleme adanmış geleneksel yollar eklemenizi sağlar; örneğin:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`blog`Burada yol, geleneksel bir *geleneksel yol* olduğundan geleneksel yönlendirme sistemini kullanır, ancak belirli bir eyleme ayrılmıştır. `controller` `action` Yol şablonunda parametre olarak görünmeyin, ancak bu yol yalnızca varsayılan değerlere sahip olabilir ve bu nedenle bu yol her zaman eyleme eşlenir `BlogController.Article` .

Rota koleksiyonundaki yollar sıralanır ve eklendikleri sırada işlenir. Bu nedenle bu örnekte yol, `blog` yol öncesinde denenmeye sunulacaktır `default` .

> [!NOTE]
> *Adanmış geleneksel yollar* genellıkle, URL yolunun kalan kısmını yakalamak için gibi **catch-all** yol parametrelerini kullanır `{*article}` . Bu, ' çok Greedy ' yolunu diğer yollarla eşleştirirken hedeflediğiniz URL 'Lerle eşleşen bir yol haline getirir. Bunu çözümlemek için ' Greedy ' yollarını daha sonra yol tablosuna koyun.

### <a name="fallback"></a>Geri dönüş

İstek işlemenin bir parçası olarak, MVC, uygulamanızdaki bir denetleyiciyi ve eylemi bulmak için yol değerlerinin kullanılabileceğini doğrular. Rota değerleri bir eylemle eşleşmezse, yol eşleşme olarak kabul edilmez ve sonraki rota denenir. Buna *geri dönüş* denir ve geleneksel yolların çakıştığı durumları basitleştirmek için tasarlanmıştır.

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

Bu denetleyici, URL yolu ve rota verileri ile eşleşen iki eylemi tanımlar `/Products/Edit/17` `{ controller = Products, action = Edit, id = 17 }` . Bu, `Edit(int)` bir ürünü düzenlemek için bir form gösteren ve `Edit(int, Product)` postalanan formu işleyen MVC denetleyicileri için tipik bir modeldir. Bunu yapmak için `Edit(int, Product)` , istek BIR http olduğunda `POST` ve `Edit(int)` http fiili başka bir şey olduğunda bu olası MVC 'nin seçim yapması gerekir.

`HttpPostAttribute`( `[HttpPost]` ), `IActionConstraint` Yalnızca http fiili olduğunda eylemin seçili olmasını sağlayan bir uygulamasıdır `POST` . ' Nin varlığı `IActionConstraint` `Edit(int, Product)` ' daha iyi ' bir eşleşme yapar `Edit(int)` , `Edit(int, Product)` Bu nedenle ilk olarak denenir.

Yalnızca özelleştirilmiş senaryolarda özel uygulamalar yazmanız gerekir `IActionConstraint` , ancak benzer öznitelikler gibi özniteliklerin rolün `HttpPostAttribute`  diğer HTTP fiilleri için tanımlandığını anlamak önemlidir. Geleneksel yönlendirmesinde, bir iş akışının parçası olduklarında aynı eylem adını kullanmak yaygın olarak karşılaşılan bir işlemdir `show form -> submit form` . Bu düzenin rahatlığı, [ıactionconstraint 'ı anlama](#understanding-iactionconstraint) bölümünde daha sonra görünür hale gelir.

Birden çok yol eşleşirse ve MVC ' en iyi ' yolu bulamazsa, bir oluşturur `AmbiguousActionException` .

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Yol adları

Dizeler  `"blog"` ve `"default"` Aşağıdaki örneklerde yol adları verilmiştir:

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

Öznitelik yönlendirme eylemleri doğrudan yönlendirme şablonlarına eşlemek için bir öznitelik kümesi kullanır. Aşağıdaki örnekte, `app.UseMvc();` `Configure` yönteminde kullanılır ve hiçbir yol geçirilir. `HomeController`Varsayılan yolun eşleşeceğini benzer bir URL kümesiyle eşleşir `{controller=Home}/{action=Index}/{id?}` :

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

`HomeController.Index()`Bu eylem, veya URL yollarının herhangi biri için yürütülür `/` `/Home` `/Home/Index` .

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
> Yukarıdaki yol şablonları,, ve için yol parametreleri `action` tanımlamaz `area` `controller` . Aslında, öznitelik rotalarında bu yol parametrelerine izin verilmez. Yol şablonu bir eylemle zaten ilişkili olduğundan, URL 'den eylem adını ayrıştırmak mantıklı değildir.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http [fiil] öznitelikleriyle öznitelik yönlendirme

Öznitelik yönlendirme, gibi öznitelikleri de kullanabilir `Http[Verb]` `HttpPostAttribute` . Bu özniteliklerin hepsi bir yol şablonunu kabul edebilir. Bu örnekte, aynı rota şablonuyla eşleşen iki eylem gösterilmektedir:

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

Bir URL yolu için, `/products` `ProductsApi.ListProducts` http fiili olduğunda `GET` ve `ProductsApi.CreateProduct` http fiili olduğunda yürütülecektir, eylem `POST` gibi bir URL yolu yürütülür. Öznitelik yönlendirme öncelikle URL ile yol öznitelikleri tarafından tanımlanan yol şablonları kümesine göre eşleşir. Bir rota şablonu eşleştiğinde, `IActionConstraint` hangi eylemlerin yürütüleceğini belirleyen kısıtlamalar uygulanır.

> [!TIP]
> Bir REST API oluştururken, `[Route(...)]` eylem tüm http yöntemlerini kabul edecek şekilde bir eylem yönteminde kullanmak isteyeceksiniz. `Http*Verb*Attributes`API 'nizin neleri desteklediği hakkında kesin olması için daha belirgin bir şekilde kullanılması daha iyidir. REST API 'lerinin istemcileri, hangi yolların ve HTTP fiillerinin belirli mantıksal işlemlere eşlendiğini bilmelidir.

Bir öznitelik yolu belirli bir eyleme uyguladığı için, yol şablonu tanımının bir parçası olarak gerekli parametreleri yapmak kolaydır. Bu örnekte, `id` URL yolunun bir parçası olarak gereklidir.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Eylem, gibi bir URL yolu için `ProductsApi.GetProduct(int)` değil, gibi BIR URL yolu için yürütülür `/products/3` `/products` . Yol şablonlarının ve ilgili seçeneklerin tam açıklaması için bkz. [yönlendirme](xref:fundamentals/routing) .

## <a name="route-name"></a>Yol adı

Aşağıdaki kod, bir *yol adı* tanımlar `Products_List` :

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Yol adları, belirli bir yolu temel alan bir URL oluşturmak için kullanılabilir. Rota adlarının, yönlendirmenin URL eşleştirme davranışına etkisi yoktur ve yalnızca URL oluşturma için kullanılır. Yol adları, uygulama genelinde benzersiz olmalıdır.

> [!NOTE]
> Bunu, isteğe bağlı () parametresini tanımlayan geleneksel *varsayılan yol* ile kontrast `id` `{id?}` . API 'Leri tam olarak belirtme özelliği, farklı eylemlere izin verilmesi ve dağıtılması gibi avantajlara sahiptir `/products` `/products/5` .

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

Bu örnekte, URL yolu `/products` eşleştirebilir `ProductsApi.ListProducts` ve URL yolu `/products/5` eşleştirebilir `ProductsApi.GetProduct(int)` . Bu eylemlerin her ikisi de `GET` ile işaretlendikleri IÇIN http ile eşleşir `HttpGetAttribute` .

Bir eyleme uygulanan `/` veya bu `~/` denetleyiciye uygulanan rota şablonlarıyla birlikte bir araya getirilen bir eyleme uygulanan rota şablonları. Bu örnek, *varsayılan rotaya* benzer bir URL yolları kümesiyle eşleşir.

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

Örneğin, gibi bir yol `blog/search/{topic}` gibi bir yol daha özeldir `blog/{*article}` . `blog/search/{topic}`Tek yapmanız gereken tek bir sıralama olduğundan, varsayılan olarak ' çalıştırmaları ' yolunu mantıksal olarak konuşuyor. Geleneksel yönlendirmeyi kullanarak, yolları istenen sırada yerleştirmekten geliştirici sorumludur.

Öznitelik rotaları, `Order` Tüm Framework yol özniteliklerinin özelliğini kullanarak bir sıra yapılandırabilir. Yollar, özelliğin artan sıralamasına göre işlenir `Order` . Varsayılan sıra `0` . Kullanarak bir yolun ayarlanması `Order = -1` , bir sipariş ayarlamamadan önce çalışır. Kullanarak bir yolun ayarlanması `Order = 1` , varsayılan yol sıralaması sonrasında çalışır.

> [!TIP]
> Uygulamasına bağlı kaçının `Order` . URL alanınız, doğru sıralama değerlerinin doğru şekilde yönlendirilmesini gerektiriyorsa, istemciler de kafa karıştırıcı olabilir. Genel öznitelik yönlendirme ' de, URL eşleştirme ile doğru yolu seçer. URL oluşturma için kullanılan varsayılan sıra çalışmıyorsa, bir geçersiz kılma olarak yol adı kullanılması, özelliği uygulamadan daha basittir `Order` .

Razor Sayfa yönlendirme ve MVC denetleyici yönlendirme bir uygulamayı paylaşır. Sayfalar için yol Razor [ Razor ve uygulama kuralları: yol sırası](xref:razor-pages/razor-pages-conventions#route-order)bölümünde yer alan bilgi başlıkları hakkında bilgiler bulabilirsiniz.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Yol şablonlarında belirteç değiştirme ([denetleyici], [eylem], [alan])

Daha kolay olması için, öznitelik rotaları bir belirteci köşeli ayraç (,) içine alarak *belirteç değiştirmeyi* destekler `[` `]` . , Ve belirteçleri, `[action]` `[area]` `[controller]` yolun tanımlandığı eylemden eylem adı, alan adı ve denetleyici adı değerleriyle değiştirilmiştir. Aşağıdaki örnekte, Eylemler, açıklamalarda açıklandığı gibi URL yollarıyla eşleşir:

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

Belirteç değişikliği, öznitelik rotaları tarafından tanımlanan yol adları için de geçerlidir. `[Route("[controller]/[action]", Name="[controller]_[action]")]` Her eylem için benzersiz bir yol adı üretir.

Sabit belirteç değiştirme sınırlayıcısıyla eşleştirmek için `[` veya  `]` karakteri tekrarlayarak (veya) bunu kaçış `[[` `]]` .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Belirteç değişimini özelleştirmek için bir parametre transformatörü kullanın

Belirteç değiştirme, bir parametre transformatörü kullanılarak özelleştirilebilir. Bir parametre transformatörü `IOutboundParameterTransformer` , parametrelerinin değerini uygular ve dönüştürür. Örneğin, özel bir `SlugifyParameterTransformer` parametre transformatörü `SubscriptionManagement` Rota değerini olarak değiştirir `subscription-management` .

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

, `RouteTokenTransformerConvention` ' Da bir seçeneği olarak kaydedilir `ConfigureServices` .

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

Birden çok yol özniteliği (uygulayan `IActionConstraint` ) bir eyleme yerleştirildiğinde, her eylem kısıtlaması, onu tanımlayan öznitelikten yol şablonuyla birleştirir.

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

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Kullanarak özel yol öznitelikleri `IRouteTemplateProvider`

Çerçevede ( `[Route(...)]` ,, vb.) sunulan tüm rota öznitelikleri, `[HttpGet(...)]` `IRouteTemplateProvider` arabirimini uygular. MVC, uygulama başlatıldığında denetleyici sınıflarında ve eylem yöntemlerinde öznitelikler arar ve `IRouteTemplateProvider` ilk rota kümesini oluşturmak için uygulamalarını kullanır.

`IRouteTemplateProvider`Kendi yol öznitelerinizi tanımlamak için öğesini uygulayabilirsiniz. Her biri `IRouteTemplateProvider` , özel bir yol şablonu, sırası ve adı ile tek bir yol tanımlamanızı sağlar:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Yukarıdaki örnekteki özniteliği, `Template` `"api/[controller]"` ne zaman uygulanacağını otomatik olarak olarak ayarlar `[MyApiController]` .

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Öznitelik yollarını özelleştirmek için uygulama modelini kullanma

*Uygulama modeli* , MVC tarafından eylemlerinizi yönlendirmek ve yürütmek için kullanılan tüm meta veriler ile başlangıçta oluşturulan bir nesne modelidir. *Uygulama modeli* , yol özniteliklerinden (aracılığıyla) toplanan tüm verileri içerir `IRouteTemplateProvider` . Yönlendirme işleminin nasıl davranacağını özelleştirmek için, Başlangıç zamanında uygulama modelini değiştirmek üzere *kurallar* yazabilirsiniz. Bu bölümde, uygulama modeli kullanılarak yönlendirmeyi özelleştirmenin basit bir örneği gösterilmektedir.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Karma yönlendirme: öznitelik yönlendirme vs geleneksel yönlendirme

MVC uygulamaları, geleneksel yönlendirme ve öznitelik yönlendirmenin kullanımını karıştırabilir. Tarayıcılar için HTML sayfalarına hizmet veren denetleyiciler için geleneksel yollar ve REST API 'Lerine hizmet veren denetleyiciler için öznitelik yönlendirme kullanılması normaldir.

Eylemler genel olarak Dolaştırılan veya Attribute olarak yönlendirilir. Bir yolu denetleyiciye koymak veya eylemi, BT özniteliği yönlendirilmesini sağlar. Öznitelik yollarını tanımlayan eylemlere geleneksel yollar üzerinden ulaşılamıyor ve bunun tersi de geçerlidir. Denetleyicideki **herhangi bir** rota özniteliği, denetleyici özniteliğindeki tüm eylemlerin yönlendirilmesini sağlar.

> [!NOTE]
> İki tür yönlendirme sisteminin ayırt edilmesini ne kadar ayırt eden, bir URL bir yol şablonuyla eşleştirdikten sonra uygulanan işlemdir. Geleneksel yönlendirmesinde, eşleşmeden yol değerleri, tüm geleneksel yönlendirilmiş eylemlerin arama tablosundan eylemi ve denetleyiciyi seçmek için kullanılır. Öznitelik yönlendirmesinde, her şablon zaten bir eylemle ilişkilendirilir ve başka bir arama gerekmez.

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin, `[Route("/dog{token}cat")]` ), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir. Bir açıklama için bkz. [kaynak kodu](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) . Daha fazla bilgi için [Bu soruna](https://github.com/dotnet/AspNetCore.Docs/issues/8197)bakın.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL oluşturma

MVC uygulamaları, eylemlere URL bağlantıları oluşturmak için yönlendirmenin URL oluşturma özelliklerini kullanabilir. URL oluşturma, kodlarınızın daha sağlam ve sürdürülebilir hale getirilmesi için sorunsuz kodlama URL 'Lerini ortadan kaldırır. Bu bölüm, MVC tarafından sunulan URL oluşturma özelliklerine odaklanır ve yalnızca URL oluşturmanın nasıl çalıştığına ilişkin temel bilgileri kapsar. URL oluşturma hakkında ayrıntılı bir açıklama için bkz. [yönlendirme](xref:fundamentals/routing) .

`IUrlHelper`Arabirim, URL oluşturma IÇIN MVC ve yönlendirme arasındaki temel altyapı parçasıdır. `IUrlHelper` `Url` Denetleyiciler, görünümler ve görünüm bileşenlerinde özelliği aracılığıyla kullanılabilir bir örnek bulacaksınız.

Bu örnekte `IUrlHelper` arabirim, `Controller.Url` başka bir eyleme YÖNELIK bir URL oluşturmak için özelliği aracılığıyla kullanılır.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Uygulama varsayılan geleneksel yolu kullanıyorsa, `url` değişkenin DEĞERI URL yol dizesi olacaktır `/UrlGeneration/Destination` . Bu URL yolu, yönlendirme değerlerini, geçerli istekten (çevresel değerler), `Url.Action` Bu değerleri geçirilen değerlerle ve bu değerlerin yol şablonuna geçirerek birleştirerek oluşturulur.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Yol şablonundaki her bir rota parametresinin değeri, değerler ve ortam değerleri ile eşleşen adlara sahip olacak şekilde değiştirilir. Bir değere sahip olmayan bir rota parametresi, varsa varsayılan bir değer kullanabilir veya isteğe bağlı ise ( `id` Bu örnekte olduğu gibi) atlanır. Gerekli yol parametresinin karşılık gelen bir değeri yoksa, URL oluşturma başarısız olur. Bir yol için URL oluşturma başarısız olursa, tüm yollar Denenene veya bir eşleşme bulunana kadar sonraki yol denenir.

`Url.Action`Yukarıdaki örneği geleneksel yönlendirmeyi varsayar, ancak URL oluşturma özniteliği farklı olsa da, öznitelik yönlendirme ile benzer şekilde çalışır. Geleneksel yönlendirme ile, bir şablonu genişletmek için yol değerleri kullanılır ve `controller` `action` genellikle bu şablonda görüntülenir. Bu, yönlendirme Ile eşleşen URL 'ler bir *kurala* bağlı olduğundan, bu işe yarar. Öznitelik yönlendirmesinde, ve için rota değerlerinin `controller` `action` şablonda görünmesine izin verilmez; bunun yerine kullanılacak şablonu aramak için kullanılır.

Bu örnek öznitelik yönlendirme kullanır:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC, tüm öznitelik yönlendirilmiş eylemlerinin bir arama tablosunu oluşturur ve `controller` `action` URL oluşturma için kullanılacak yol şablonunu seçmek için ve değerleriyle eşleştirecektir. Yukarıdaki örnekte   `custom/url/to/destination` oluşturulur.

### <a name="generating-urls-by-action-name"></a>Eylem adına göre URL 'Leri oluşturma

`Url.Action` (`IUrlHelper` . `Action`) ve tüm ilgili aşırı yüklemeler, bir denetleyici adı ve eylem adı belirterek ne bağlandığınızı belirtmek istediğiniz fikri temel alır.

> [!NOTE]
> Kullanırken `Url.Action` , için geçerli yol değerleri `controller` ve `action` değeri için belirtilir `controller` ve `action` her iki *çevresel değerin* **ve** *değerin* bir parçasıdır. Yöntemi `Url.Action` her zaman ve ' nin geçerli değerlerini kullanır `action` `controller` ve geçerli eyleme yönlendiren bir URL yolu oluşturacaktır.

Yönlendirme, bir URL oluştururken sağlamadığınız bilgileri doldurmanızı sağlamak için çevresel değerlerde değerleri kullanmayı dener. `{a}/{b}/{c}/{d}`Yönlendirme ve ortam değerleri gibi bir yol kullanarak `{ a = Alice, b = Bob, c = Carol, d = David }` , yönlendirmenin tüm rota parametreleri bir değere sahip olduğundan, ek değer olmadan bir URL oluşturmak için yeterli bilgi vardır. Değeri eklediyseniz `{ d = Donovan }` değer `{ d = David }` yok sayılır ve oluşturulan URL yolu da olur `Alice/Bob/Carol/Donovan` .

> [!WARNING]
> URL yolları hiyerarşiktir. Yukarıdaki örnekte, değerini eklediyseniz, `{ c = Cheryl }` her iki değer de `{ c = Carol, d = David }` yok sayılır. Bu durumda artık için bir değer yoktur `d` ve URL oluşturma başarısız olur. İstenen değerini `c` ve ' i belirtmeniz gerekir `d` .  Varsayılan yol () ile bu soruna yaklaşmanız gerekebilir, `{controller}/{action}/{id?}` ancak `Url.Action` her zaman açıkça bir ve değeri belirtmek için uygulamada bu davranış hakkında nadiren karşılaşacaksınız `controller` `action` .

Daha uzun aşırı yüklemeleri `Url.Action` Ayrıca, ve dışındaki rota parametreleri için değerler sağlamak üzere ek bir *yol değerleri* nesnesi de alır `controller` `action` . Bunun en yaygın olarak bu şekilde kullanıldığını görürsünüz `id` `Url.Action("Buy", "Products", new { id = 17 })` . Kurala göre *yol değerleri* nesnesi genellikle anonim türdeki bir nesnedir, ancak bir `IDictionary<>` veya *düz olarak eski .net nesnesi* de olabilir. Yol parametreleriyle eşleşmeyen ek rota değerleri sorgu dizesine konur.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Mutlak URL oluşturmak için şunu kabul eden bir aşırı yükleme kullanın `protocol` : `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Rotaya göre URL oluşturma

Yukarıdaki kod, denetleyiciyi ve eylem adını geçirerek bir URL oluşturmayı göstermiştir. `IUrlHelper` Ayrıca `Url.RouteUrl` Yöntem ailesini de sağlar. Bu yöntemler öğesine benzerdir `Url.Action` , ancak geçerli değerlerini `action` ve yol değerlerini kopyalamalardır `controller` . En yaygın kullanım, genellikle bir denetleyici veya eylem *adı belirtmeden,* URL oluşturmak için belirli bir yolu kullanmak üzere bir yol adı belirtmektir.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML 'de URL oluşturma

`IHtmlHelper` , ve `HtmlHelper` `Html.BeginForm` `Html.ActionLink` öğelerini sırasıyla oluşturmak ve oluşturmak için yöntemler sağlar `<form>` `<a>` . Bu yöntemler `Url.Action` BIR URL oluşturmak için yöntemini kullanır ve benzer bağımsız değişkenleri kabul ederler. `Url.RouteUrl`İçin `HtmlHelper` şirkeme, `Html.BeginRouteForm` ve `Html.RouteLink` benzer işlevlere sahiptir.

Taghelmakalar, `form` taghelper ve `<a>` taghelper aracılığıyla URL 'ler oluşturur. Bunların her ikisi de `IUrlHelper` kendi uygulamaları için kullanılır. Daha fazla bilgi için bkz. [formlarla çalışma](../views/working-with-forms.md) .

Görünümler içinde, `IUrlHelper` `Url` yukarıda yer almayan herhangi BIR geçici URL oluşturma özelliği aracılığıyla kullanılabilir.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Eylem sonuçlarında URL oluşturma

Yukarıdaki örnekler `IUrlHelper` bir denetleyicide kullanılarak gösteriliyor, ancak denetleyicideki en yaygın kullanım, bir eylem sonucunun parçası olarak BIR URL oluşturmak olur.

`ControllerBase`Ve `Controller` temel sınıflar, başka bir eyleme başvuruda bulunan eylem sonuçları için kolay yöntemler sağlar. Tipik bir kullanım, Kullanıcı girişi kabul edildikten sonra yeniden yönlendirilmelidir.

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

Eylem sonuçları Fabrika yöntemleri, üzerindeki yöntemlere benzer bir model izler `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Adanmış geleneksel yollar için özel durum

Geleneksel yönlendirme, *adanmış geleneksel yol* olarak adlandırılan özel bir yol tanımı türünü kullanabilir. Aşağıdaki örnekte, adlı yol `blog` ayrılmış bir geleneksel yoldur.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Bu yol tanımlarının kullanılması, `Url.Action("Index", "Home")` yol Ile URL yolunu oluşturacak `/` `default` , ancak neden? Yol değerlerinin `{ controller = Home, action = Index }` BIR URL oluşturmak için yeterli olacağını tahmin edebilirsiniz `blog` ve sonuç olur `/blog?action=Index&controller=Home` .

Adanmış geleneksel yollar, URL oluşturmayla "çok Greedy" olmasını önleyen karşılık gelen bir yol parametresi olmayan varsayılan değerlerin özel bir davranışına bağımlıdır. Bu durumda, varsayılan değerler `{ controller = Blog, action = Article }` , `controller` ya da `action` yol parametresi olarak görünmez. Yönlendirme URL oluşturma işlemi gerçekleştirdiğinde, belirtilen değerler varsayılan değerlerle eşleşmelidir. `blog`Değerler eşleşmediğinden, KULLANıLARAK URL oluşturma başarısız olur `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` . Sonra yeniden yönlendirme işlemi denemeye geri döner `default` ve başarılı olur.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Alanlar

[Bölgeler](areas.md) , ilgili işlevselliği ayrı bir yönlendirme-ad alanı (denetleyici eylemleri için) ve klasör yapısı (görünümler için) olarak bir grupla düzenlemek için kullanılan bir MVC özelliğidir. Alanların kullanılması, bir uygulamanın farklı *alanlara* sahip oldukları sürece aynı ada sahip birden çok denetleyicisi olmasına olanak sağlar. Alanların kullanılması, ve ' ye başka bir rota parametresi ekleyerek yönlendirmenin amacı için bir hiyerarşi `area` oluşturur `controller` `action` . Bu bölüm, yönlendirmenin alanlarla nasıl etkileşime gireceğini tartışır. alanların görünümlerle nasıl kullanıldığı hakkında ayrıntılar için bkz. [alanlara](areas.md) bakın.

Aşağıdaki örnek, MVC 'yi varsayılan geleneksel yolu ve adlı bir alan için bir *alan yolu* kullanacak şekilde yapılandırır `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Benzer bir URL yolu eşleştirilirken `/Manage/Users/AddUser` , ilk yol yol değerlerini oluşturur `{ area = Blog, controller = Users, action = AddUser }` . `area`Yol değeri, `area` tarafından oluşturulan yolun aşağıdaki değere eşit olduğu aslında için varsayılan bir değer tarafından üretilir `MapAreaRoute` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute``area`Bu durumda, belirtilen alan adını kullanmak için hem varsayılan bir değer hem de kısıtlama kullanarak bir yol oluşturur `Blog` . Varsayılan değer, yolun her zaman `{ area = Blog, ... }` oluşturulmasını sağlar, KıSıTLAMA `{ area = Blog, ... }` URL oluşturma için değeri gerektirir.

> [!TIP]
> Geleneksel yönlendirme sıra bağımlıdır. Genel olarak, alanlar içeren rotalar, alan olmayan rotalardan daha belirgin olduklarından daha önce rota tablosuna yerleştirilmelidir.

Yukarıdaki örneği kullanarak, yol değerleri aşağıdaki eylemle eşleşir:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

, Bir `AreaAttribute` alanın parçası olarak denetleyiciyi belirtir, bu denetleyicinin alanında olduğunu varsayalım `Blog` . Özniteliği olmayan denetleyiciler `[Area]` hiçbir alanın üyesi değildir  ve `area` Rota değeri yönlendirme tarafından sağlandığında eşleşmeyecektir. Aşağıdaki örnekte, yalnızca listelenen ilk denetleyici rota değerleriyle eşleştirebilir `{ area = Blog, controller = Users, action = AddUser }` .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Her denetleyicinin ad alanı, tamamlanma için burada gösterilir. Aksi takdirde, denetleyicilerde adlandırma çakışması olur ve derleyici hatası oluşturur. Sınıf ad alanlarının MVC 'nin yönlendirme üzerinde hiçbir etkisi yoktur.

İlk iki denetleyici alanların üyeleridir ve yalnızca ilgili alan adı rota değeri tarafından sağlandığında eşleşir `area` . Üçüncü denetleyici hiçbir alanın üyesi değildir ve yalnızca Yönlendirme tarafından hiçbir değer sağlanmamışsa eşleşemez `area` .

> [!NOTE]
> Değer *olmadan* eşleşme açısından değerin yokluğu değeri `area` `area` null ya da boş dize olarak aynıdır.

Bir alan içinde bir eylem yürütürken, için rota değeri, `area` URL oluşturma için kullanılmak üzere yönlendirme için bir *ortam değeri* olarak kullanılabilir olacaktır. Bu, varsayılan olarak, aşağıdaki örnekte gösterildiği gibi, URL oluşturma için *yapışkan* olarak hareket ettiği anlamına gelir.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Iactionconstraint 'i anlama

> [!NOTE]
> Bu bölüm, Framework iç işlevleri hakkında ayrıntılı bir bakış ve MVC 'nin yürütülecek eylemi nasıl seçtiği. Tipik bir uygulamanın özel olması gerekmez `IActionConstraint`

Arabirime tanıdık olmasanız bile büyük olasılıkla zaten kullandık `IActionConstraint` . `[HttpGet]`Özniteliği ve benzer `[Http-VERB]` öznitelikleri, `IActionConstraint` bir eylem yönteminin yürütülmesini sınırlandırmak için uygular.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Varsayılan geleneksel yol varsayılıyor, URL yolu `/Products/Edit` `{ controller = Products, action = Edit }` burada gösterilen eylemlerin **her ikisiyle de** eşleşen değerleri üretir. `IActionConstraint`Terminolojisinde, her ikisi de rota verileriyle eşleşen her iki eylemin da aday kabul edileceğini söyliyoruz.

`HttpGetAttribute`Yürütüldüğünde, *Düzenle ()* , *Get* için BIR eşleşmedir ve diğer http fiili için bir eşleşme değildir. `Edit(...)`Eylemin tanımlı hiçbir kısıtlaması yok, bu nedenle herhangi BIR http fiili ile eşleşir. Bu nedenle, yalnızca bir eşleşme olduğunu varsayıyoruz `POST` `Edit(...)` . Ancak, `GET` her iki eylem de eşleşemez, ancak bir eylem, olmadan bir eylemden `IActionConstraint` her zaman *daha iyi* kabul edilebilir. Bu nedenle `Edit()` `[HttpGet]` , daha belirli bir kabul edildiğinden ve her iki eylem de eşleşeceğinden seçilecek.

Kavramsal olarak, `IActionConstraint` bir *aşırı yükleme* biçimidir, ancak aynı ada sahip yöntemlerin aşırı YÜKLENMESI yerine aynı URL ile eşleşen eylemler arasında aşırı yükleme yapılır. Öznitelik yönlendirme Ayrıca, `IActionConstraint` farklı denetleyicilerle her ikisi de olarak kabul edilen eylemlere neden olabilir.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Iactionconstraint uygulama

' Nin uygulanmasının en kolay yolu, `IActionConstraint` ' dan türetilmiş bir sınıf oluşturmaktır `System.Attribute` ve bunları eylemleriniz ve denetleyicilerinize yerleştirmelidir. MVC, öznitelik olarak uygulanan her türlü otomatik olarak keşfedilir `IActionConstraint` . Kısıtlama uygulamak için uygulama modelini kullanabilirsiniz ve bu, büyük olasılıkla en esnek yaklaşımdır ve bu sayede, nasıl uygulanabileceğini meta programlayabilirsiniz.

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

Yöntemi uygulamaktan `Accept` ve kısıtlamanın yürütülmesi için bir ' Order ' seçmeye sorumlusunuz. Bu durumda, yöntemi, `Accept` `true` yol değeri eşleştiğinde eylemin bir eşleşme olduğunu belirtmek için öğesini döndürür `country` . Bu, öğesinden farklı bir `RouteValueAttribute` eyleme geri dönüş yapılmasına izin verir. Örnek, bir eylem tanımlarsanız, `en-US` gibi bir ülke kodunun `fr-FR` , uygulanmamış daha fazla genel denetleyiciye geri dönemeyeceğini gösterir `[CountrySpecific(...)]` .

`Order`Özelliği kısıtlamanın parçası olan *aşamayı* belirler. Eylem kısıtlamaları, gruplarına göre gruplar içinde çalışır `Order` . Örneğin, tüm Framework tarafından sunulan HTTP yöntemi öznitelikleri aynı `Order` aşamada çalışacak şekilde aynı değeri kullanır. İstediğiniz ilkeleri uygulamak için ihtiyacınız olan çok sayıda aşamaya sahip olabilirsiniz.

> [!TIP]
> Bir değere karar vermek için, `Order` kısıtlamalarınızın http yöntemlerinden önce uygulanıp uygulanmayacağı hakkında düşünün. Daha az sayı önce çalışır.

::: moniker-end
