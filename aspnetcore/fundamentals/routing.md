---
title: ASP.NET Çekirdeğinde Yönlendirme
author: rick-anderson
description: ASP.NET Core yönlendirmenin HTTP isteklerini eşleştirmekten ve çalıştırılabilir uç noktalara göndermekten nasıl sorumlu olduğunu keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
uid: fundamentals/routing
ms.openlocfilehash: 5742ac6879ce46e01247ddd2f8bfe3e3b8a2a02a
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751152"
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Çekirdeğinde Yönlendirme

Ryan [Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-3.0"

Yönlendirme, gelen HTTP isteklerini eşleştirmekten ve bu istekleri uygulamanın yürütülebilir uç noktalarına göndermekten sorumludur. [Uç noktalar,](#endpoint) uygulamanın çalıştırılabilir istek işleme kodu birimleridir. Uç noktalar uygulamada tanımlanır ve uygulama başladığında yapılandırılır. Uç nokta eşleştirme işlemi, isteğin URL'sinden değerleri ayıklayabilir ve istek işleme için bu değerleri sağlayabilir. Uygulamadan gelen uç nokta bilgilerini kullanarak yönlendirme, uç noktalara eşlenen URL'ler de oluşturabilir.

Uygulamalar yönlendirmeyi şu kullanarak yapılandırabilir:

- Denetleyiciler
- Razor Pages
- SignalR
- gRPC Hizmetleri
- [Sistem Durumu Denetimleri](xref:host-and-deploy/health-checks)gibi uç nokta etkin [ara yazılım.](xref:fundamentals/middleware/index)
- Delegeler ve lambdas yönlendirme ile kayıtlı.

Bu belge, ASP.NET Core yönlendirmesinin alt düzey ayrıntılarını kapsar. Yönlendirme yapılandırma hakkında bilgi için:

* Denetleyiciler için <xref:mvc/controllers/routing>bkz.
* Razor Pages kuralları için <xref:razor-pages/razor-pages-conventions>bkz.

Bu belgede açıklanan uç nokta yönlendirme sistemi, Core 3.0 ve sonraki ASP.NET için geçerlidir. Dayalı önceki yönlendirme sistemi hakkında bilgi <xref:Microsoft.AspNetCore.Routing.IRouter>için, aşağıdaki yaklaşımlardan birini kullanarak ASP.NET Core 2.1 sürümünü seçin:

* Önceki sürümün sürüm seçici.
* [Core 2.1 yönlendirmeASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)seçin.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Bu belgenin karşıdan yükleme örnekleri `Startup` belirli bir sınıf tarafından etkinleştirilir. Belirli bir örneği çalıştırmak *Program.cs* için, istenen `Startup` sınıfı aramak için Program.cs değiştirin.

## <a name="routing-basics"></a>Yönlendirme temelleri

Tüm ASP.NET Core şablonları oluşturulan kodda yönlendirme içerir. Yönlendirme, [middleware](xref:fundamentals/middleware/index) boru hattında `Startup.Configure`kayıtlıdır.

Aşağıdaki kod yönlendirmenin temel bir örneğini gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Yönlendirme tarafından kayıtlı bir ara yazılım <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> çifti <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>kullanır ve:

* `UseRouting`ara yazılım boru hattına eşleşen rota ekler. Bu ara yazılım, uygulamada tanımlanan uç nokta kümesine bakar ve isteğe göre [en iyi eşleşmeyi](#urlm) seçer.
* `UseEndpoints`ara yazılım ardışık noktasına uç nokta yürütme ekler. Seçili bitiş noktasıyla ilişkili temsilciyi çalıştırZ.

Önceki örnek, [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) yöntemini kullanarak kod bitiş noktasına giden tek bir *rotayı* içerir:

* Bir HTTP `GET` isteği kök URL'ye `/`gönderildiğinde:
  * Gösterilen istek temsilcisi yürütülür.
  * `Hello World!`HTTP yanıtına yazılır. Varsayılan olarak, kök `/` `https://localhost:5001/`URL.
* İstek yöntemi değilse `GET` veya kök URL `/`değilse, rota eşleşmeleri yok ve http 404 döndürülür.

### <a name="endpoint"></a>Uç Nokta

<a name="endpoint"></a>

Yöntem `MapGet` bir **bitiş noktası**tanımlamak için kullanılır. Bitiş noktası şu olabilir:

* URL ve HTTP yöntemini eşleştirerek seçilir.
* Temsilci çalıştırılarak yürütülür.

Uygulama tarafından eşleşip çalıştırılabilen uç noktalar `UseEndpoints`. Örneğin, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, ve [benzer yöntemler](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) istek temsilcilerini yönlendirme sistemine bağlar.
Core çerçeve özelliklerini yönlendirme sistemine bağlamak için ek yöntemler ASP.NET kullanılabilir:
- [Razor Sayfalar için MapRazorPages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [Denetleyiciler için MapControllers](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [SignalR\<için MapHub THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> gRPC için](xref:grpc/aspnetcore)

Aşağıdaki örnekte, daha karmaşık bir rota şablonuyla yönlendirme gösterilmektedir:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Dize `/hello/{name:alpha}` bir **rota şablonudur.** Bitiş noktasının nasıl eşleştinin yapılandırılması için kullanılır. Bu durumda, şablon eşleşir:

* Bir URL gibi`/hello/Ryan`
* Alfabetik karakter `/hello/` dizisi ile başlayan herhangi bir URL yolu.  `:alpha`yalnızca alfabetik karakterlerle eşleşen bir rota kısıtlaması uygular. [Rota kısıtlamaları](#route-constraint-reference) daha sonra bu belgede açıklanır.

URL yolunun ikinci bölümü: `{name:alpha}`

* Parametreye `name` bağlıdır.
* Yakalanır ve [httpRequest.RouteValues'te](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)saklanır.

Bu belgede açıklanan uç nokta yönlendirme sistemi, Core 3.0 ASP.NET itibariyle yenidir. Ancak, ASP.NET Core'un tüm sürümleri aynı rota şablonu özelliklerini ve rota kısıtlamalarını destekler.

Aşağıdaki örnek, [sistem durumu denetimleri](xref:host-and-deploy/health-checks) ve yetkilendirme ile yönlendirmeyi gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Önceki örnek, aşağıdakileri gösterir:

* Yetkilendirme ara ware yönlendirme ile kullanılabilir.
* Uç noktalar yetkilendirme davranışını yapılandırmak için kullanılabilir.

Arama, <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> bir sistem durumu denetimi bitiş noktası ekler. Bu <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> çağrıya zincirleme, bitiş noktasına bir yetkilendirme ilkesi iliştiriyor.

Arama <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> ve kimlik doğrulama ve yetkilendirme ara ware ekler. Bu ara yazılımlar <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` arasına yerleştirilir ve böylece şunlar arasında dır:

* Hangi bitiş noktasının `UseRouting`seçili olduğunu görün.
* Bitiş noktasına gönderilmeden önce <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> bir yetkilendirme ilkesi uygulayın.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Uç nokta meta verileri

Önceki örnekte, iki uç nokta vardır, ancak yalnızca sistem durumu denetimi bitiş noktasının bir yetkilendirme ilkesi eklenmiş olması vardır. İstek, sistem durumu denetimi bitiş `/healthz`noktasıyla eşleşiyorsa, yetkilendirme denetimi gerçekleştirilir. Bu, uç noktaların kendilerine eklenmiş ekstra veri olabileceğini gösterir. Bu ekstra veri uç nokta **meta veri**denir:

* Meta veriler yönlendirmeye duyarlı ara yazılımlarla işlenebilir.
* Meta veriler herhangi bir .NET türünden olabilir.

## <a name="routing-concepts"></a>Yönlendirme kavramları

Yönlendirme sistemi, güçlü **uç nokta** konseptini ekleyerek ara yazılım boru hattının üzerine inşa eder. Uç noktalar, yönlendirme, yetkilendirme ve ASP.NET Core sistemlerinin herhangi bir sayısı açısından uygulamanın işlevselliğinin birbirinden farklı birimlerini temsil eder.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>ASP.NET Temel uç nokta tanımı

ASP.NET Core bitiş noktası:

* Çalıştırılabilir: Bir <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.
* Genişletilebilir: Meta [veri](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) koleksiyonu vardır.
* Seçilebilir: İsteğe bağlı olarak, [yönlendirme bilgileri](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)vardır.
* Sayısal: Uç noktalarının toplanması <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> [DI'den](xref:fundamentals/dependency-injection)alınarak listelenebilir.

Aşağıdaki kod, geçerli istekle eşleşen bitiş noktasının nasıl alınıp denetlenerek alınabildiğini gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Bitiş noktası seçilirse, `HttpContext`'den alınabilir. Özellikleri kontrol edilebilir. Uç nokta nesneleri değişmezdir ve oluşturulduktan sonra değiştirilemez. Bitiş noktasının en yaygın <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>türü bir . `RouteEndpoint`yönlendirme sistemi tarafından seçilmesini sağlayan bilgileri içerir.

Önceki kodda, [uygulama. Kullanın](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) sıralı bir [ara yazılım](xref:fundamentals/middleware/index)yapılandırır.

<a name="mt"></a>

Aşağıdaki kod, ardışık alanda `app.Use` çağrıldığına bağlı olarak bir bitiş noktası olmayabileceğini gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Bu önceki örnek, bir bitiş noktası seçilip seçilmediğini gösteren ifadeler ekler. `Console.WriteLine` Netlik için, örnek sağlanan `/` bitiş noktasına bir görüntü adı atar.

Bu kodu bir EKRAN `/` URL'si ile çalıştırma:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Bu kodu diğer URL ekranlarıyla çalıştırma:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Bu çıktı şunu göstermektedir:

* Bitiş noktası her zaman `UseRouting` null önce denir.
* Bir eşleşme bulunursa, bitiş noktası ile `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.'nin arasında null olmayan
* Bir `UseEndpoints` eşleşme bulunduğunda ara yazılım **terminaldir.** [Terminal ara yazılımları](#tm) daha sonra bu belgede tanımlanır.
* Yalnızca eşleşme `UseEndpoints` bulunamayınca çalıştırdıktan sonra ara yazılım.

Ara `UseRouting` yazılım, bitiş noktasını geçerli içeriğe eklemek için [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) yöntemini kullanır. Aracıyı `UseRouting` özel mantıkla değiştirmek ve yine de uç noktaları kullanmanın avantajlarından yararlanmak mümkündür. Uç noktalar ara yazılım gibi düşük düzeyli bir ilkeldir ve yönlendirme uygulamasıyla birleştiğinde değildir. Çoğu uygulamanın özel mantıkla değiştirilmesi `UseRouting` gerekmez.

Ara `UseEndpoints` yazılım, `UseRouting` ara yazılımla birlikte kullanılmak üzere tasarlanmıştır. Bir bitiş noktası yürütmek için temel mantık karmaşık değildir. Bitiş <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> noktasını almak ve sonra özelliğini <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> çağırmak için kullanın.

Aşağıdaki kod, ara yazılımların yönlendirmeyi nasıl etkileyebileceğini veya buna tepki verebileceğini gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Önceki örnek iki önemli kavram gösterir:

* Ara yazılım, `UseRouting` yönlendirmenin üzerinde çalıştığı verileri değiştirmek için önce çalıştırılabilir.
    * Yönlendirmeden önce görünen genellikle ara yazılım, isteğin bazı özelliğini <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>değiştirir, <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>örneğin , , veya .
* Middleware arasında `UseRouting` ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> bitiş noktası yürütülmeden önce yönlendirme sonuçlarını işlemek için çalıştırabilirsiniz.
    * Arasında `UseRouting` çalışan ara `UseEndpoints`yazılım:
      * Genellikle bitiş noktalarını anlamak için meta verileri inceler.
      * Genellikle güvenlik kararları verir, `UseAuthorization` tarafından `UseCors`yapılan ve .
    * Ara yazılım ve meta verilerin birleşimi, son nokta başına ilkelerin yapılandırılmasına olanak tanır.

Önceki kod, uç nokta başına ilkeleri destekleyen özel bir ara yazılım örneğini gösterir. Ara yazılım, konsola hassas verilere erişimin *denetim günlüğünü* yazar. Ara yazılım, `AuditPolicyAttribute` meta verilerle bir bitiş noktasını *denetlemek* üzere yapılandırılabilir. Bu örnek, yalnızca hassas olarak işaretlenmiş uç noktaların denetlendiği bir *kabul* deseni gösterir. Bu mantığı terste tanımlamak, örneğin güvenli olarak işaretlenmeyen her şeyi denetlemek mümkündür. Uç nokta meta veri sistemi esnektir. Bu mantık, kullanım örneğine uygun şekilde tasarlanabilir.

Önceki örnek kod uç noktalarının temel kavramlarını göstermek için tasarlanmıştır. **Örnek üretim kullanımı için tasarlanmamıştır.** Bir *denetim günlüğü* ara ware daha eksiksiz bir sürümü olacaktır:

* Bir dosyaya veya veritabanına oturum açın.
* Kullanıcı, IP adresi, hassas bitiş noktasının adı ve daha fazlası gibi ayrıntıları ekleyin.

Denetim ilkesi meta `AuditPolicyAttribute` verileri, `Attribute` denetleyiciler ve SignalR gibi sınıf tabanlı çerçevelerle daha kolay kullanım için bir veri olarak tanımlanır. *Koda rota*kullanırken:

* Meta veriler bir oluşturucu API ile eklenir.
* Sınıf tabanlı çerçeveler, uç noktaları oluştururken ilgili yöntem ve sınıf tüm öznitelikleri içerir.

Meta veri türleri için en iyi uygulamalar, bunları arabirim veya öznitelikler olarak tanımlamakiçindir. Arabirimler ve öznitelikler kodun yeniden kullanılmasına izin verir. Meta veri sistemi esnektir ve herhangi bir sınırlama getirmez.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Terminal ara yazılımlarını ve yönlendirmeyi karşılaştırma

Aşağıdaki kod örneği, ara yazılım kullanarak yönlendirme ile tezat eder:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Middleware ile `Approach 1:` gösterilen tarzı **terminal middleware**olduğunu. Eşleşen bir işlem yaptığı için buna terminal ara yazılım denir:

* Önceki örnekteki eşleşen işlem `Path == "/"` ara yazılım ve `Path == "/Movie"` yönlendirme içindir.
* Bir eşleşme başarılı olduğunda, `next` ara yazılımı çağırmak yerine bazı işlevleri yürütür ve döndürür.

Aramayı sonlandırdığı, bazı işlevleri yürüttüğü ve sonra döndürdettiği için buna terminal ara ware denir.

Terminal ara yazılımlarını ve yönlendirmeyi karşılaştırma:
* Her iki yaklaşım da işleme ardışık ardışık ardışık işleminin sonlandırmasını sağlar:
    * Middleware yerine çağıran dönerek boru hattı `next`sona erer.
    * Uç noktalar her zaman terminaldir.
* Terminal ara yazılım, ara yazılımın ardışık alanda rasgele bir yerde konumlandırılamasına olanak tanır:
    * Uç noktalar <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>. konumunda yürütülür
* Terminal ara yazılım, ara yazılımın ne zaman eşleştiğini belirlemek için rasgele kod sağlar:
    * Özel rota eşleştirme kodu ayrıntılı olabilir ve doğru yazmak zor.
    * Yönlendirme, tipik uygulamalar için basit çözümler sağlar. Çoğu uygulama özel rota eşleştirme kodu gerektirmez.
* Gibi ara yazılım ile `UseAuthorization` uç `UseCors`noktaları arayüzü ve .
    * Yetkilendirme sistemi yle `UseAuthorization` bir `UseCors` terminal ara ware kullanma veya el ile interfacing gerektirir.

[Bitiş noktası](#endpoint) her ikisini de tanımlar:

* İstekleri işlemek için bir temsilci.
* Rasgele meta veri koleksiyonu. Meta veriler, her bitiş noktasına bağlı ilkelere ve yapılandırmaya dayalı çapraz kesme endişelerini uygulamak için kullanılır.

Terminal ara yazılım etkili bir araç olabilir, ancak şunları gerektirebilir:

* Önemli miktarda kodlama ve test.
* İstenilen esneklik düzeyine ulaşmak için diğer sistemlerle manuel entegrasyon.

Bir terminal ara yazılım yazmadan önce yönlendirme ile entegre düşünün.

[Harita](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) ile tümleşen veya <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> genellikle yönlendirme farkında uç noktasına dönüştürülebilen mevcut terminal ara ware. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) yönlendirici-ware için desen gösterir:
* <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>Bir uzantı yöntemi yazın.
* <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>Kullanarak iç içe bir ara yazılım ardışık alanı oluşturun.
* Aracı yeni boru hattına takın. Bu durumda, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>bir <xref:Microsoft.AspNetCore.Http.RequestDelegate>içine middleware boru hattı .
* Yeni `Map` ara yazılım boru hattını arayın ve sağlayın.
* Uzantı `Map` yöntemiyle sağlanan oluşturucu nesneyi döndürün.

Aşağıdaki kod [MapHealthChecks](xref:host-and-deploy/health-checks)kullanımını gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Önceki örnek, oluşturucu nesneyi döndürmenin neden önemli olduğunu gösterir. Oluşturucu nesnesini döndürmek, uygulama geliştiricisinin bitiş noktası için yetkilendirme gibi ilkeleri yapılandırmasına olanak tanır. Bu örnekte, sistem denetimleri aracı nın yetkilendirme sistemiyle doğrudan tümleştirmesi yoktur.

Meta veri sistemi terminal ara yazılım kullanarak genişletilebilirlik yazarları tarafından karşılaşılan sorunlara yanıt olarak oluşturuldu. Her ara yazılımın yetkilendirme sistemiyle kendi tümleştirmesini uygulaması sorunludur.

<a name="urlm"></a>

### <a name="url-matching"></a>URL eşleştirme

* Yönlendirmenin gelen istekle bitiş [noktasına eşleşmesi](#endpoint)işlemidir.
* URL yolundaki verilere ve üstbilgilere dayanır.
* İstekteki herhangi bir veriyi dikkate almak için uzatılabilir.

Bir yönlendirme ara ware yürütüldüğünde, `Endpoint` geçerli istekteki bir istek <xref:Microsoft.AspNetCore.Http.HttpContext> [özelliğine](xref:fundamentals/request-features) bir ve rota değerleri ayarlar:

* [HttpContext.GetEndpoint'i](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) aramak bitiş noktasını alır.
* `HttpRequest.RouteValues`rota değerlerinin toplanmasını alır.

Yönlendirme ara ware sonra çalışan [middleware](xref:fundamentals/middleware/index) bitiş noktasını incelemek ve harekete geçebilirsiniz. Örneğin, bir yetkilendirme ara ware bir yetkilendirme ilkesi için bitiş noktası meta veri toplama sorgulayabilir. İstek işleme ardışık ardışık ardışık tüm ara yazılım yürütüldükten sonra, seçilen bitiş noktasının temsilcisi çağrılır.

Uç nokta yönlendirmedeki yönlendirme sistemi tüm sevk kararlarından sorumludur. Ara yazılım, seçili bitiş noktasına dayalı ilkeler uyguladığından, aşağıdakilerin olması önemlidir:

* Gönderme veya güvenlik ilkelerinin uygulanmasını etkileyebilecek herhangi bir karar yönlendirme sistemi içinde verilir.

> [!WARNING]
> Geriye dönük uyumluluk için, bir Denetleyici veya Razor Pages bitiş noktası temsilcisi yürütüldüğünde, [RouteContext.RouteData'nın](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özellikleri şimdiye kadar gerçekleştirilen istek işlemedayalı olarak uygun değerlere ayarlanır.
>
> Tür, `RouteContext` gelecekteki bir sürümde eski olarak işaretlenir:
>
> * 'ye `RouteData.Values` `HttpRequest.RouteValues`geçirin.
> * Uç `RouteData.DataTokens` nokta meta verilerinden [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) almak için geçirin.

URL eşleştirme, yapılandırılabilir bir aşama lar kümesinde çalışır. Her aşamada, çıkış eşleşmeler kümesidir. Maç seti bir sonraki aşamaya kadar daha da daraltılabilir. Yönlendirme uygulaması, uç noktaları eşleştirmek için bir işlem emri garanti etmez. **Olası tüm** eşleşmeler aynı anda işlenir. URL eşleştirme aşamaları aşağıdaki sırada gerçekleşir. ASP.NET Core:

1. URL yolunu uç noktalar kümesine ve rota şablonlarına göre işler ve **tüm** eşleşmeleri toplar.
1. Önceki listeyi alır ve uygulanan rota kısıtlamalarıyla başarısız olan eşleşmeleri kaldırır.
1. Önceki listeyi alır ve [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) örnekleri kümesinde başarısız olan eşleşmeleri kaldırır.
1. Önceki listeden son kararı vermek için [EndpointSelector'u](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) kullanır.

Uç noktaların listesine göre öncelik verilir:

* [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Rota şablonu önceliği](#rtp)

Eşleşen tüm uç noktalar, ulaşılana <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> kadar her aşamada işlenir. Son `EndpointSelector` aşama. En iyi eşleşme olarak maçlardan en yüksek öncelikli bitiş noktasını seçer. En iyi eşleşmeyle aynı önceliğe sahip başka eşleşmeler varsa, belirsiz bir eşleşme özel durumu atılır.

Rota önceliği, daha yüksek bir öncelik verilen **daha özel** bir rota şablonuna göre hesaplanır. Örneğin, şablonları `/hello` göz `/{message}`önünde bulundurun ve:

* Her ikisi de `/hello`URL yoluna eşleşir.
* `/hello`daha spesifik ve bu nedenle daha yüksek önceliktir.

Genel olarak, rota önceliği uygulamada kullanılan URL şemaları türleri için en iyi eşleşmeyi seçme iyi bir iş yapar. Belirsizliği önlemek için yalnızca gerektiğinde kullanın. <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>

Yönlendirme tarafından sağlanan genişletilebilirlik türleri nedeniyle, yönlendirme sisteminin belirsiz yolları önceden hesaplaması mümkün değildir. Rota şablonları `/{message:alpha}` gibi bir örnek `/{message:int}`düşünün ve:

* Kısıtlama `alpha` yalnızca alfabetik karakterlerle eşleşir.
* Kısıtlama `int` yalnızca sayılarla eşleşir.
* Bu şablonlar aynı rota önceliğine sahiptir, ancak her ikisinin de eşleşen tek bir URL'si yoktur.
* Yönlendirme sistemi başlangıçta bir belirsizlik hatası bildirdiyse, bu geçerli kullanım örneğini engeller.

> [!WARNING]
>
> İçerideki <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> işlemlerin sırası, bir istisna dışında yönlendirme davranışını etkilemez. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>ve <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> çağrıldıkları sıraya göre uç noktalarına otomatik olarak bir sipariş değeri atayın. Bu, eski yönlendirme uygulamalarıyla aynı garantileri sağlayan yönlendirme sistemi olmadan denetleyicilerin uzun süreli davranışını simüle eder.
>
> Yönlendirmenin eski uygulamasında, yolların işlendiği sıraya bağımlı olan yönlendirme genişletilebilirliğini uygulamak mümkündür. Core 3.0 ve sonraki ASP.NET'da bitiş noktası yönlendirmesi:
> 
> * Güzergah kavramı yok.
> * Sipariş garantisi sağlamaz. Tüm uç noktalar aynı anda işlenir.
>
> Bu, eski yönlendirme sistemini kullanarak sıkışıp kaldığınız anlamına geliyorsa, [yardım için bir GitHub sorunu açın.](https://github.com/dotnet/aspnetcore/issues)

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Rota şablonu önceliği ve bitiş noktası seçim sırası

[Rota şablonu önceliği,](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) her rota şablonu ne kadar özel olduğuna bağlı bir değer atayan bir sistemdir. Rota şablonu önceliği:

* Sık karşılaşılan durumlarda uç noktaların sırasını ayarlama gereksinimini önler.
* Yönlendirme davranışının sağduyulu beklentilerini karşılamaya çalışır.

Örneğin, şablonları `/Products/List` ve `/Products/{id}`. Bu URL yolu `/Products/List` `/Products/{id}` `/Products/List`için daha iyi bir maç olduğunu varsaymak makul olacaktır. Edebi segment `/List` parametre segmentinden `/{id}`daha iyi bir önceliğe sahip olarak kabul edilir çünkü çalışır .

Öncelik çalışmalarının ayrıntıları, rota şablonlarının nasıl tanımlandığıyla birleştiğinde yapılır:

* Daha fazla segmente sahip şablonlar daha spesifik olarak kabul edilir.
* Gerçek metnin içeren bir kesimi, parametre kesiminden daha spesifik olarak kabul edilir.
* Kısıtlamalı bir parametre kesimi, olmayan bir parametre den daha özel olarak kabul edilir.
* Karmaşık bir segment, kısıtlamalı bir parametre kesimi olarak özel olarak kabul edilir.
* Tüm parametreleri yakalamak en az spesifiktir.

Tam değerlere başvurmak için [GitHub'daki kaynak koduna](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) bakın.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>URL oluşturma kavramları

URL oluşturma:

* Yönlendirmenin bir rota değerleri kümesine dayalı bir URL yolu oluşturabileceği işlemdir.
* Uç noktalar ve bunlara erişen URL'ler arasında mantıksal bir ayrım yapılmasına olanak tanır.

Uç nokta yönlendirme, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API'yi içerir. `LinkGenerator`[DI'den](xref:fundamentals/dependency-injection)edinilebilen tek tonlu bir hizmettir. API, `LinkGenerator` yürütme isteği bağlamının dışında kullanılabilir. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) ve Tag <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> [Helpers,](xref:mvc/views/tag-helpers/intro)HTML Yardımcıları ve [Eylem Sonuçları](xref:mvc/controllers/actions)gibi, bağlantı `LinkGenerator` oluşturma yetenekleri sağlamak için dahili olarak API kullanın güveniyor senaryolar.

Bağlantı jeneratörü bir **adres** ve adres **şemaları**kavramı tarafından desteklenen. Adres düzeni, bağlantı oluşturma için göz önünde bulundurulması gereken uç noktaları belirlemenin bir yoludur. Örneğin, birçok kullanıcının denetleyicilerden aşina olduğu rota adı ve rota değerleri senaryoları ve Ustura Sayfaları adres düzeni olarak uygulanır.

Bağlantı jeneratörü aşağıdaki uzatma yöntemleri ile denetleyicilere ve Razor Pages'e bağlanabilir:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Bu yöntemlerin aşırı yükleri `HttpContext`içeren bağımsız değişkenleri kabul eder. Bu yöntemler işlevsel olarak [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) ve [Url.Page'e](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)eşdeğerdir, ancak ek esneklik ve seçenekler sunar.

Yöntemler `GetPath*` en çok `Url.Action` benzer `Url.Page`ve , onlar mutlak bir yol içeren bir URI oluşturmak. Yöntemler `GetUri*` her zaman bir şema ve ana bilgisayar içeren mutlak bir URI oluşturur. Yürütme isteği bağlamında `HttpContext` bir URI oluşturmayı kabul eden yöntemler. Ortam [ambient](#ambient) rotası değerleri, URL temel yolu, düzen ve yürütme isteğinden ana bilgisayar geçersiz kılınmadığı sürece kullanılır.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>bir adresle çağrılır. URI oluşturma iki adımda gerçekleşir:

1. Adres, adresle eşleşen uç noktaların listesine bağlıdır.
1. Her uç nokta, <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> sağlanan değerlerle eşleşen bir rota deseni bulunana kadar değerlendirilir. Elde edilen çıkış, bağlantı jeneratörüne verilen diğer URI parçalarıyla birleştirilir ve döndürülür.

Her tür <xref:Microsoft.AspNetCore.Routing.LinkGenerator> adres için standart bağlantı oluşturma yeteneklerini destekleyerek sağlanan yöntemler. Bağlantı jeneratörü kullanmak için en uygun yolu belirli bir adres türü için işlemleri gerçekleştirmek uzantısı yöntemleri geçer:

| Uzatma Yöntemi | Açıklama |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Sağlanan değerlere dayalı mutlak bir yol ile bir URI oluşturur. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Sağlanan değerlere dayalı mutlak bir URI oluşturur.             |

> [!WARNING]
> Arama <xref:Microsoft.AspNetCore.Routing.LinkGenerator> yöntemlerinin aşağıdaki etkilerine dikkat edin:
>
> * Gelen `GetUri*` isteklerin üstbilgisini `Host` doğrulamayan bir uygulama yapılandırmasında uzantı yöntemlerini dikkatli kullanın. Gelen `Host` isteklerin üstbilgisi doğrulanmazsa, güvenilmeyen istek girişi bir görünüm veya sayfada UrI'lerde istemciye geri gönderilebilir. Tüm üretim uygulamalarının üstbilgiyi `Host` bilinen geçerli değerlere göre doğrulamak için sunucularını yapılandırmalarını öneririz.
>
> * Middleware'de dikkatli <xref:Microsoft.AspNetCore.Routing.LinkGenerator> kullanın `Map` veya `MapWhen`. `Map*`bağlantı oluşturma çıktısını etkileyen yürütme isteğinin temel yolunu değiştirir. Tüm API'ler <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bir temel yol belirtmeye izin verir. Bağlantı oluşturma üzerindeki etkiyi `Map*` geri almak için boş bir temel yol belirtin.

### <a name="middleware-example"></a>Middleware örneği

Aşağıdaki örnekte, ara yazılım, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> depo ürünlerini listeleyen bir eylem yöntemine bağlantı oluşturmak için API'yi kullanır. Bağlantı jeneratörü bir sınıfa enjekte ederek `GenerateLink` kullanma ve bir uygulamadaki herhangi bir sınıf için arama kullanılabilir:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Rota şablonu başvurusu

Yol eşleştirilirse bağlanan rota parametrelerini `{}` tanımlar. Bir rota segmentinde birden fazla rota parametresi tanımlanabilir, ancak rota parametreleri gerçek bir değerle ayrılmalıdır. Örneğin, `{controller=Home}{action=Index}` geçerli bir yol değildir, çünkü ile `{controller}` . `{action}`  Rota parametrelerinin bir adı olmalıdır ve ek öznitelikler belirtilmiş olabilir.

Rota parametreleri dışındaki gerçek metin `{id}`(örneğin, ) `/` ve yol ayırıcısı URL'deki metyle eşleşmelidir. Metin eşleştirme büyük/küçük harf duyarsız ve URL'ler yolunun deşifre gösterimine dayanır. Bir literal rota parametresi `{` delimiter maç veya `}`, karakteri yineleyerek delimiter kaçış. Örneğin `{{` ya `}}`da .

Yıldız veya `*` çift yıldız `**`işareti :

* URI'nin geri kalanına bağlamak için rota parametresine önek olarak kullanılabilir.
* **Catch-all** parametreleri denir. Örneğin, `blog/{**slug}`:
  * Ile başlayan ve `/blog` onu izleyen herhangi bir değere sahip herhangi bir URI eşleşir.
  * Aşağıdaki `/blog` değer [sümüklü böcek](https://developer.mozilla.org/docs/Glossary/Slug) rota değerine atanır.

Tümlerini yakalama parametreleri boş dizeyle de eşleşebilir.

Tümlerini yakalama parametresi, yol ayırıcı `/` karakterleri de dahil olmak üzere bir URL oluşturmak için kullanıldığında uygun karakterlerden kaçar. Örneğin, rota `foo/{*path}` değerleri `{ path = "my/path" }` ile rota `foo/my%2Fpath`oluşturur. Kaçan ileri eğik çizgiye dikkat edin. Gidiş-dönüş yol ayırıcı karakterleri için `**` rota parametresi önekini kullanın. Üreten `foo/{**path}` `{ path = "my/path" }` `foo/my/path`rota.

İsteğe bağlı dosya uzantısı ile bir dosya adını yakalamaya çalışan URL desenleri ek hususlar vardır. Örneğin, şablonu `files/{filename}.{ext?}`göz önünde bulundurun. Her ikisi `filename` için `ext` değerler ve var olduğunda, her iki değer de doldurulur. URL'de yalnızca `filename` bir değer varsa, rota eşleşir, `.` çünkü izleme isteğe bağlıdır. Aşağıdaki URL'ler bu rotayla eşleşir:

* `/files/myFile.txt`
* `/files/myFile`

Rota parametreleri, eşitler işaretiyle ayrılan parametre adı ()`=`ile ayrıldıktan sonra varsayılan değeri belirterek varsayılan **değerlere** sahip olabilir. Örneğin, `{controller=Home}` varsayılan `Home` değer olarak `controller`tanımlar. Parametrenin URL'sinde değer yoksa varsayılan değer kullanılır. Rota parametreleri, parametre adının sonuna`?`bir soru işareti ( ) ekleyerek isteğe bağlı hale getirilir. Örneğin, `id?`. İsteğe bağlı değerler ve varsayılan rota parametreleri arasındaki fark:

* Varsayılan değeri olan bir rota parametresi her zaman bir değer üretir.
* İsteğe bağlı parametrenin değeri yalnızca istek URL'si tarafından bir değer sağlandığında yapılır.

Rota parametrelerinde URL'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamalar olabilir. Rota `:` parametre adı sonra ekleme ve kısıtlama adı bir rota parametresi üzerinde satır içinde kısıtlama belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, kısıtlama adından `(...)` sonra parantez içinde direnir. Birden çok *satır içi kısıtlamalar* başka `:` bir ve kısıtlama adı ekleyerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, URL işlemede kullanılacak bir örnek oluşturmak için <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> hizmete aktarılır. Örneğin, rota şablonu `blog/{article:minlength(10)}` bağımsız `minlength` değişkenile `10`bir kısıtlama belirtir. Rota kısıtlamaları ve çerçeve tarafından sağlanan kısıtlamaların listesi hakkında daha fazla bilgi için [Rota kısıtlaması başvuru](#route-constraint-reference) bölümüne bakın.

Rota parametrelerinde parametre transformatörleri de olabilir. Parametre transformatörleri bağlantılar oluştururken ve eylemleri ve sayfaları URL'lere eşleştirirken bir parametrenin değerini dönüştürür. Kısıtlamalar gibi, parametre transformatörleri de rota parametresi adından sonra bir `:` ve transformatör adı eklenerek rota parametresine satır satır eklenebilir. Örneğin, rota şablonu `blog/{article:slugify}` bir `slugify` transformatör belirtir. Parametre transformatörleri hakkında daha fazla bilgi için [Parametre transformatör referans](#parameter-transformer-reference) bölümüne bakın.

Aşağıdaki tabloörnek rota şablonlarını ve davranışlarını gösterir:

| Rota Şablonu                           | Örnek Eşleşen URI    | İstek URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Sadece tek yol `/hello`eşleşir.                                     |
| `{Page=Home}`                            | `/`                     | Maçlar ve `Page` `Home`ayarlar .                                         |
| `{Page=Home}`                            | `/Contact`              | Maçlar ve `Page` `Contact`ayarlar .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` Denetleyici ve `List` eylem haritaları.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 123'e ayarlanmış `Products` denetleyicive `Details` eyleme eşler.`id` |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` Denetleyici ve `Index` yönteme eşler. `id` yoksayılır.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | `Products` Denetleyici ve `Index` yönteme eşler. `id` yoksayılır.        |

Şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar da rota şablonu dışında belirtilebilir.

### <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık [segmentler, açgözlü olmayan](#greedy) bir şekilde sağdan sola doğru gerçek sınırlayıcılar eşleştirilerek işlenir. Örneğin, `[Route("/a{b}c{d}")]` karmaşık bir segmenttir.
Karmaşık segmentler, bunları başarıyla kullanabilmek için anlaşılması gereken belirli bir şekilde çalışır. Bu bölümdeki örnek, karmaşık kesimlerin yalnızca sınırlayıcı metin parametre değerlerinin içinde görünmediğinde neden gerçekten iyi çalıştığını göstermektedir. Bir [regex](/dotnet/standard/base-types/regular-expressions) kullanarak ve daha sonra değerleri el ile ayıklama daha karmaşık durumlarda için gereklidir.

Bu, yönlendirmenin şablon `/a{b}c{d}` ve URL yolu `/abcd`ile gerçekleştirdiği adımların bir özetidir. Algoritmanın `|` nasıl çalıştığını görselleştirmeye yardımcı olmak için kullanılır:

* İlk edebi, sağdan sola, `c`. Yani `/abcd` sağdan aranır `/ab|c|d`ve bulur .
* Sağdaki her`d`şey ( ) şimdi rota `{d}`parametresi ile eşleşir.
* Bir sonraki gerçek, sağdan `a`sola, . Kaldığımız `/ab|c|d` yerden başlayarak arandı, sonra `a` bulundu. `/|a|b|c|d`
* Sağdaki değer (`b`) şimdi rota parametresi `{b}`ile eşleşir.
* Kalan metin ve kalan rota şablonu yoktur, bu nedenle bu bir eşleşmedir.

Aşağıda, aynı şablonu `/a{b}c{d}` ve URL yolunu `/aabcd`kullanan olumsuz bir durum örneği verilmiştir. Algoritmanın `|` nasıl çalıştığını görselleştirmeye yardımcı olmak için kullanılır. Bu durum aynı algoritma ile açıklanan bir eşleşme değildir:
* İlk edebi, sağdan sola, `c`. Yani `/aabcd` sağdan aranır `/aab|c|d`ve bulur .
* Sağdaki her`d`şey ( ) şimdi rota `{d}`parametresi ile eşleşir.
* Bir sonraki gerçek, sağdan `a`sola, . Kaldığımız `/aab|c|d` yerden başlayarak arandı, sonra `a` bulundu. `/a|a|b|c|d`
* Sağdaki değer (`b`) şimdi rota parametresi `{b}`ile eşleşir.
* Bu noktada kalan metin, `a`ancak algoritma ayrıştırmak için rota şablonu tükendi, bu yüzden bu bir eşleşme değildir.

Eşleşen algoritma [açgözlü olmayan](#greedy)olduğundan:

* Her adımda mümkün olan en küçük metin miktarıyla eşleşir.
* Parametre değerlerinin içinde delimiter değerinin göründüğü herhangi bir durum eşleşmez.

Normal ifadeler eşleşen davranışları üzerinde çok daha fazla denetim sağlar.

<a name="greedy"></a>

Açgözlü eşleştirme, aynı zamanda [tembel eşleştirme](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)olarak bilinen, mümkün olan en büyük dize eşleşir. Açgözlü olmayan, mümkün olan en küçük dizeyle eşleşir.

## <a name="route-constraint-reference"></a>Rota kısıtlaması başvurusu

Gelen URL'de bir eşleşme oluştuğunda ve URL yolu rota değerlerine belirtildiğinde rota kısıtlamaları yürütülür. Rota kısıtlamaları genellikle rota şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda doğru veya yanlış bir karar verir. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilemeyeceğini değerlendirmek için rota değerinin dışındaki verileri kullanır. Örneğin, http <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> fiiline dayalı bir isteği kabul edebilir veya reddedebilir. Kısıtlamalar yönlendirme istekleri ve bağlantı oluşturma kullanılır.

> [!WARNING]
> Giriş doğrulama için kısıtlamalar kullanmayın. Giriş doğrulaması için kısıtlamalar kullanılıyorsa, geçersiz giriş `404` bulunamadı yanıtıyla sonuçlanır. Geçersiz giriş, uygun `400` bir hata iletisi içeren bir Kötü İstek oluşturmalıdır. Rota kısıtlamaları, belirli bir rotanın girdilerini doğrulamak için değil, benzer yolları ayrıştırmak için kullanılır.

Aşağıdaki tabloörnek rota kısıtlamaları ve bunların beklenen davranışlarını gösterir:

| kısıtlama | Örnek | Örnek Eşleşmeler | Notlar |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir karşıcıyla eşleşir |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` Kibrit `false`ler ya da . Duyarlı |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Değişmez kültürdeki geçerli `DateTime` bir değerle eşleşir. Önceki uyarıya bakın. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Değişmez kültürdeki geçerli `decimal` bir değerle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `double` bir değerle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `float` bir değerle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Geçerli `Guid` bir değerle eşleşir |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli `long` bir değerle eşleşir |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String en az 4 karakter olmalıdır |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Dize en fazla 8 karakter olmalıdır |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String tam olarak 12 karakter uzunluğunda olmalıdır |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String en az 8 ve en fazla 16 karakter uzunluğunda olmalıdır |
| `min(value)` | `{age:min(18)}` | `19` | İntesayı değeri en az 18 olmalıdır |
| `max(value)` | `{age:max(120)}` | `91` | Tümsek değeri 120'den fazla olmamalıdır |
| `range(min,max)` | `{age:range(18,120)}` | `91` | İntesayı değeri en az 18 olmalı, ancak en fazla 120 |
| `alpha` | `{name:alpha}` | `Rick` | String bir veya daha fazla alfabetik karakter `a` - `z` ve büyük/küçük harf duyarsız oluşmalıdır. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String normal ifadeyle eşleşmelidir. Normal bir ifade tanımlama yla ilgili ipuçlarına bakın. |
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre dışı bir değerin mevcut olduğunu zorlamak için kullanılır |

[!INCLUDE[](~/includes/regex.md)]

Birden çok, kolon sınırlı kısıtlamalar tek bir parametre uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle sınırlandırMaz:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL'yi doğrulayan ve CLR türüne dönüştürülen rota kısıtlamaları her zaman değişmez kültürü kullanır. Örneğin, CLR türüne `int` dönüştürme `DateTime`veya . Bu kısıtlamalar, URL'nin yerelleştirilebilir olmadığını varsayar. Çerçeve tarafından sağlanan rota kısıtlamaları, rota değerlerinde depolanan değerleri değiştirmez. URL'den ayrıştırılan tüm rota değerleri dizeleri olarak depolanır. Örneğin, `float` kısıtlama rota değerini bir yüzer'e dönüştürmeyi dener, ancak dönüştürülen değer yalnızca bir şamandıraya dönüştürülebileceğini doğrulamak için kullanılır.

### <a name="regular-expressions-in-constraints"></a>Kısıtlamalardaki normal ifadeler

[!INCLUDE[](~/includes/regex.md)]

Normal ifadeler, rota kısıtlamasını `regex(...)` kullanarak satır içinde kısıtlamalar olarak belirtilebilir. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Ailedeki yöntemler de kısıtlamaların bir nesne edebi kabul eder. Bu form kullanılırsa, dize değerleri normal ifadeler olarak yorumlanır.

Aşağıdaki kod satır içinde regex kısıtlaması kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Aşağıdaki kod, bir regex kısıtlaması belirtmek için bir nesne literal kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

ASP.NET Core çerçevesi `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` düzenli ifade oluşturucuekler. Bu <xref:System.Text.RegularExpressions.RegexOptions> üyelerin tanımı için bkz.

Normal ifadeler, yönlendirme ve C# dili tarafından kullanılanlara benzer sınır dışı layıcılar ve belirteçler kullanır. Normal ifade belirteçleri kaçmış olmalıdır. Satır satır kısıtlamasında normal ifadeyi `^\d{3}-\d{2}-\d{4}$` kullanmak için aşağıdakilerden birini kullanın:

* Dize kaçış karakterinden `\\` kaçmak için dizede sağlanan karakterleri C# kaynak dosyasında karakter olarak değiştirin. `\` `\`
* [Verbatim dize literals](/dotnet/csharp/language-reference/keywords/string).

Yönlendirme parametre delimiter karakterleri kaçmak `{` `}`için `[` `]`, , , , ifade karakterleri `{{`çift, `[[` `]]`örneğin, , , `}}`, . Aşağıdaki tablo düzenli bir ifade ve onun kaçan sürümünü gösterir:

| Normal ifade    | Kaçan normal ifade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmede kullanılan normal ifadeler genellikle `^` karakterle başlar ve dizebaşlangıç konumuyla eşleşir. İfadeler genellikle `$` karakterle sona erer ve dize sonuyla eşleşir. Ve `^` `$` karakterler, normal ifadenin tüm rota parametre değeriyle eşleşmesini sağlar. `^` Ve `$` karakterler olmadan, normal ifade genellikle istenmeyen dize içinde herhangi bir alt dize eşleşir. Aşağıdaki tablo örnekler sağlar ve neden eşleştiklerini veya eşleşmediklerini açıklar:

| İfadeler   | Dize    | Eşleştirme | Açıklama               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | Mz        | Evet   | İfadeyle eşleşir    |
| `[a-z]{2}`   | MZ        | Evet   | Servis talebine duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | `^` Bkz. `$` ve üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | `^` Bkz. `$` ve üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için [bkz.](/dotnet/standard/base-types/regular-expression-language-quick-reference)

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` `action` yalnızca rota değeri `list`ile `get`eşleşir . `create` Kısıtlamalar sözlüğüne aktarılırsa, `^(list|get|create)$` dize eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyan kısıtlamalar sözlüğünde geçirilen kısıtlamalar da normal ifadeler olarak kabul edilir. Bilinen kısıtlamalardan biriyle eşleşmeyan bir şablon içinde geçirilen kısıtlamalar normal ifadeler olarak kabul edilmez.

### <a name="custom-route-constraints"></a>Özel rota kısıtlamaları

<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> Arayüz uygulanarak özel rota kısıtlamaları oluşturulabilir. `IRouteConstraint` Arabirim, <xref:System.Web.Routing.IRouteConstraint.Match*>kısıtlama `true` memnun veya `false` aksi takdirde döndürür içerir.

Özel rota kısıtlamalarına nadiren ihtiyaç duyular. Özel bir rota kısıtlaması uygulamadan önce, model bağlama gibi alternatifleri göz önünde bulundurun.

Özel `IRouteConstraint`bir kullanım için, rota kısıtlaması türü servis <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> kapsayıcısında uygulamanın kine kaydedilmelidir. A, `ConstraintMap` kısıtlama anahtarlarını bu `IRouteConstraint` kısıtlamaları doğrulayan uygulamalara yönlendiren bir sözlüktür. Bir uygulama, `ConstraintMap` hizmetlerin bir `Startup.ConfigureServices` parçası olarak [güncellenebilir. Çağrı ekleme](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan . `services.Configure<RouteOptions>` Örneğin:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Önceki kısıtlama aşağıdaki kodda uygulanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Rota parametresine uygulanmasını `MyCustomConstraint` engeller: `0`

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Yukarıdaki kod:

* Rotanın `0` segmentinde `{id}` önler.
* Özel bir kısıtlama uygulamanın temel bir örneğini sağlamak için gösterilir. Bir üretim uygulamasında kullanılmamalıdır.

Aşağıdaki kod, bir içeren bir `id` `0` işlenmeyi önlemek için daha iyi bir yaklaşımdır:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Önceki kod yaklaşım üzerinde aşağıdaki `MyCustomConstraint` avantajlara sahiptir:

* Özel bir kısıtlama gerektirmez.
* Rota parametresi . `0`

## <a name="parameter-transformer-reference"></a>Parametre transformatör referansı

Parametre transformatörleri:

* Kullanarak bir bağlantı oluştururken çalıştırın. <xref:Microsoft.AspNetCore.Routing.LinkGenerator>
* Uygulayın. <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>
* 'yi kullanarak <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>yapılandırılır.
* Parametrenin rota değerini alın ve yeni bir dize değerine dönüştürün.
* Oluşturulan bağlantıda dönüştürülen değeri kullanarak sonuçlanır.

Örneğin, rota `slugify` deseni `blog\{article:slugify}` özel `Url.Action(new { article = "MyTestArticle" })` bir parametre `blog\my-test-article`transformatörü üretir.

Aşağıdaki `IOutboundParameterTransformer` uygulamayı göz önünde bulundurun:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Bir rota deseninde bir parametre transformatörü `Startup.ConfigureServices`kullanmak için, şu şekilde kullanarak <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> yapılandırın:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core çerçevesi, bitiş noktasının çözüldüğü yerlerde URI'yi dönüştürmek için parametre transformatörleri kullanır. `area`Örneğin, parametre transformatörleri , , `controller` `action`, ve `page`.

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Önceki rota şablonuyla, `SubscriptionManagementController.GetAll` eylem URI `/subscription-management/get-all`ile eşleşir. Parametre transformatörü, bağlantı oluşturmak için kullanılan rota değerlerini değiştirmez. Örneğin, `Url.Action("GetAll", "SubscriptionManagement")` `/subscription-management/get-all`çıktılar.

ASP.NET Core, oluşturulan rotalarla parametre transformatörleri kullanmak için API kuralları sağlar:

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC kuralı, uygulamadaki tüm öznitelik rotalarına belirli bir parametre transformatörü uygular. Parametre transformatörü, bağlantı yolu belirteçlerini değiştirildikleri gibi dönüştürür. Daha fazla bilgi için bkz: [Belirteç değiştirmesini özelleştirmek için bir parametre transformatörü kullanın.](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)
* Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API kuralını kullanır. Bu kural, otomatik olarak keşfedilen tüm Razor Sayfalar için belirtilen bir parametre transformatörü uygular. Parametre transformatörü, Razor Pages yollarının klasör ve dosya adı bölümlerini dönüştürür. Daha fazla bilgi için bkz. [Sayfa rotalarını özelleştirmek için bir parametre transformatörü kullanın.](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)

<a name="ugr"></a>

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Bu bölümde, URL oluşturma tarafından uygulanan algoritma için bir başvuru içerir. Uygulamada, URL oluşturmanın en karmaşık örnekleri denetleyicileri veya Razor Pages kullanır. Ek bilgi için [denetleyicilerde yönlendirmeye](xref:mvc/controllers/routing) bakın.

URL oluşturma işlemi [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) veya benzer bir yöntem için bir çağrı ile başlar. Yöntem, bir adres, bir rota değerleri kümesi ve isteğe bağlı `HttpContext`olarak geçerli istek hakkında bilgi ile sağlanır.

İlk adım, adresin türüyle eşleşen bir sözcük [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) kullanarak bir aday bitiş noktası kümesini çözmek için adresi kullanmaktır.

Bir url oluşturma işlemi başarılı olana kadar, aday kümesi adres şeması tarafından bulunduktan sonra, uç noktalar sıralanır ve yinelemeli olarak işlenir. URL oluşturma belirsizlikleri kontrol **etmez,** ilk sonuç nihai sonuç döndü.

### <a name="troubleshooting-url-generation-with-logging"></a>Günlük ile URL oluşturma sorun giderme

Sorun giderme URL oluşturmanın ilk adımı, `Microsoft.AspNetCore.Routing` `TRACE`günlük düzeyini . `LinkGenerator`sorunları gidermek için yararlı olabilecek işleme hakkında birçok ayrıntı kaydeder.

URL oluşturma yla ilgili ayrıntılar için [URL oluşturma başvurusuna](#ugr) bakın.

### <a name="addresses"></a>Adresler

Adresler, bir aramayı bir dizi aday uç noktaya bağlamak için kullanılan URL oluşturma kavramıdır.

Adresler varsayılan olarak iki uygulamayla birlikte gelen genişletilebilir bir kavramdır:

* Adres olarak uç`string`nokta *adı* ( ) kullanma:
    * MVC'nin rota adına benzer işlevsellik sağlar.
    * <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> Meta veri türünü kullanır.
    * Sağlanan dizeyi, tüm kayıtlı uç noktaların meta verilerine göre çözer.
    * Birden çok uç nokta aynı adı kullanıyorsa, başlangıç için bir özel durum oluşturur.
    * Denetleyiciler ve Jilet Sayfaları dışında genel amaçlı kullanım için önerilir.
* Adres olarak<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>rota *değerlerini* ( ) kullanma:
    * Denetleyicilere ve Razor Pages eski URL üretimine benzer işlevler sağlar.
    * Uzatmak ve hata ayıklamak için çok karmaşık.
    * , Tag Helpers, HTML Yardımcıları, Eylem Sonuçları, vb. tarafından `IUrlHelper`kullanılan uygulamayı sağlar.

Adres şemasının rolü, adres ve eşleşen uç noktalar arasındaki ilişkiyi rasgele ölçütlere göre yapmaktır:

* Bitiş noktası adı düzeni temel sözlük araması gerçekleştirir.
* Rota değerleri düzeni, küme algoritmasının karmaşık en iyi alt kümesine sahiptir.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Ortam değerleri ve açık değerler

Geçerli istekten yönlendirme, geçerli isteğin `HttpContext.Request.RouteValues`rota değerlerine erişiyor. Geçerli istekle ilişkili değerler **ortam değerleri**olarak adlandırılır. Açıklık amacıyla, dokümantasyon açık **değerler**olarak yöntemlere geçirilen rota değerlerini ifade eder.

Aşağıdaki örnekte ortam değerleri ve açık değerler gösterilmektedir. Geçerli istek ve açık değerlerden ortam değerleri `{ id = 17, }`sağlar: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Yukarıdaki kod:

* Döndürür`/Widget/Index/17`
* DI <xref:Microsoft.AspNetCore.Routing.LinkGenerator> [DI](xref:fundamentals/dependency-injection)üzerinden alır .

Aşağıdaki kod hiçbir ortam değerleri ve açık `{ controller = "Home", action = "Subscribe", id = 17, }`değerler sağlar::

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Önceki yöntem döndürür`/Home/Subscribe/17`

İadelerde `WidgetController` `/Widget/Subscribe/17`aşağıdaki kod:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Aşağıdaki kod, denetleyiciyi geçerli istekteki ortam değerlerinden ve `{ action = "Edit", id = 17, }`açık değerlerden sağlar: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Yukarıdaki kodda:

* `/Gadget/Edit/17`döndürülür.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>alır <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
eylem yöntemi için mutlak bir yol olan bir URL oluşturur. URL belirtilen `action` adı ve `route` değerleri içerir.

Aşağıdaki kod, geçerli istek ve açık değerlerden `{ page = "./Edit, id = 17, }`ortam değerleri sağlar: :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Önceki kod, `url` Razor `/Edit/17` Sayfasını Edit Sayfası'nın aşağıdaki sayfa yönergesini içerdiği zamana göre belirlenir:

 `@page "{id:int}"`

Edit sayfası rota şablonu `"{id:int}"` içermiyorsa. `url` `/Edit?id=17`

MVC'nin <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> davranışı, burada açıklanan kurallara ek olarak bir karmaşıklık katmanı ekler:

* `IUrlHelper`her zaman ortam değerleri olarak geçerli istek ten rota değerlerini sağlar.
* [iUrlHelper.Action,](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) geliştirici tarafından `action` `controller` geçersiz kılınmadığı sürece geçerli ve rota değerlerini her zaman açık değerler olarak kopyalar.
* [iUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) geçersiz kılınmadığı `page` sürece geçerli rota değerini her zaman açık bir değer olarak kopyalar. <!--by the user-->
* `IUrlHelper.Page`geçersiz kılınmadığı `handler` sürece, `null` geçerli rota değerini açık değerlerle her zaman geçersiz kılar.

MVC kendi kurallarına uymak gibi görünmüyor, çünkü kullanıcılar genellikle ortam değerlerinin davranış ayrıntıları tarafından şaşırır. Tarihsel ve uyumluluk nedenleriyle, bazı `action`rota `controller` `page`değerleri `handler` gibi , , , ve kendi özel durum davranışı var.

Uyumluluk `IUrlHelper` için bu `LinkGenerator.GetPathByAction` `LinkGenerator.GetPathByPage` anormallikleri sağlayan ve yineler eşdeğer işlevsellik.

### <a name="url-generation-process"></a>URL oluşturma süreci

Aday uç noktaları kümesi bulunduktan sonra, URL oluşturma algoritması:

* Uç noktaları yinelemeli olarak işler.
* İlk başarılı sonucu verir.

Bu işlemin ilk adımı **rota değeri geçersiz liği**olarak adlandırılır.  Rota değeri geçersiz liği, yönlendirmenin ortam değerlerinden hangi rota değerlerinin kullanılması na karar verdiği ve hangisini yoksayması gerektiğine karar verdiği işlemdir. Her ortam değeri kabul edilir ve açık değerlerle birlikte veya yoksayılır.

Ortam değerlerinin rolünü düşünmenin en iyi yolu, bazı genel durumlarda uygulama geliştiricileri yazarak kaydetmeye çalışmalarıdır. Geleneksel olarak, ortam değerlerinin yararlı olduğu senaryolar MVC ile ilgilidir:

* Aynı denetleyicideki başka bir eyleme bağlanırken denetleyici adının belirtilmesi gerekmez.
* Aynı alandaki başka bir denetleyiciye bağlanırken, alan adının belirtilmesi gerekmez.
* Aynı eylem yöntemine bağlantı verirken, rota değerlerinin belirtilmesi gerekmez.
* Uygulamanın başka bir bölümüne bağlantı verirken, uygulamanın o bölümünde hiçbir anlamı olmayan rota değerlerini taşımak istemezsinüz.

Aramalar `LinkGenerator` veya `IUrlHelper` bu `null` dönüş genellikle rota değeri geçersizliği anlamama neden olur. Sorunu çözüp çözmediğini görmek için rota değerlerinden daha fazlasını açıkça belirterek yol değeri geçersizliğini giderin.

Rota değeri geçersizliği, uygulamanın URL düzeninin soldan sağa bir hiyerarşiyle hiyerarşik olduğu varsayımıyla çalışır. Uygulamada nasıl çalıştığına `{controller}/{action}/{id?}` dair sezgisel bir fikir elde etmek için temel denetleyici rota şablonuna göz önünde bulundurun. Değer **change** değişikliği, sağa görünen tüm rota değerlerini **geçersiz k.** Bu hiyerarşi hakkında varsayımı yansıtır. Uygulama için `id`bir ortam değeri varsa ve işlem için farklı bir `controller`değer belirtir:

* `id`'nin solunda `{controller}` olduğu için `{id?}`yeniden kullanılmaz.

Bu ilkeyi gösteren bazı örnekler:

* Açık değerler için `id`bir değer içeriyorsa, `id` ortam değeri yoksayılır. Ortam değerleri için `controller` ve `action` kullanılabilir.
* Açık değerler için `action`bir değer `action` içeriyorsa, herhangi bir ortam değeri yoksayılır. Ortam değerleri `controller` kullanılabilir. Açık değer için `action` ortam değeri `action`farklıise, `id` değer kullanılmaz.  Açık `action` değer için ortam değeri ile `action`aynıise, `id` değer kullanılabilir.
* Açık değerler için `controller`bir değer `controller` içeriyorsa, herhangi bir ortam değeri yoksayılır. Açık değer ortam `controller` değerinden `controller`farklıysa, `action` ve `id` değerleri kullanılmaz. Açık `controller` değer için ortam değeri ile aynı `controller`ise, `action` ve `id` değerleri kullanılabilir.

Bu süreç, öznitelik yollarının ve özel konvansiyonel yolların varlığı yla daha da karmaşık hale gelen bir süreçtir. Rota parametrelerini `{controller}/{action}/{id?}` kullanarak bir hiyerarşi belirtin gibi geleneksel yolları denetle. Özel [konvansiyonel rotalar](xref:mvc/controllers/routing#dcr) ve denetleyicilere ve Razor Pages'e [öznitelik rotaları](xref:mvc/controllers/routing#ar) için:

* Rota değerleri hiyerarşisi vardır.
* Şablonda görünmezler.

Bu gibi durumlarda, URL oluşturma **gerekli değerler** kavramını tanımlar. Denetleyiciler ve Razor Pages tarafından oluşturulan uç noktalar, rota değeri geçersiz liği çalışmasına izin veren değerleri belirtmiştir.

Rota değeri geçersizleştirme algoritması ayrıntılı olarak:

* Gerekli değer adları rota parametreleri ile birleştirilir ve daha sonra soldan sağa doğru işlenir.
* Her parametre için ortam değeri ve açık değer karşılaştırılır:
    * Ortam değeri ve açık değer aynıysa, işlem devam ediyor.
    * Ortam değeri varsa ve açık değer yoksa, URL oluştururken ortam değeri kullanılır.
    * Ortam değeri yoksa ve açık değer varsa, ortam değerini ve sonraki tüm ortam değerlerini reddedin.
    * Ortam değeri ve açık değer varsa ve iki değer farklıysa, ortam değerini ve sonraki tüm ortam değerlerini reddedin.

Bu noktada, URL oluşturma işlemi rota kısıtlamalarını değerlendirmeye hazırdır. Kabul edilen değerler kümesi, kısıtlamalara sağlanan parametre varsayılan değerleriyle birleştirilir. Kısıtlamaların tümü geçerse, işlem devam eder.

Ardından, **kabul edilen değerler** rota şablonu genişletmek için kullanılabilir. Rota şablonu işlenir:

* Soldan sağa.
* Her parametrenin kabul edilen değeri yerine geçer.
* Aşağıdaki özel durumlarda:
  * Kabul edilen değer bir değer eksikse ve parametrenin varsayılan değeri varsa, varsayılan değer kullanılır.
  * Kabul edilen değerler bir değer eksikse ve parametre isteğe bağlıysa, işleme devam edilir.
  * Eksik isteğe bağlı parametrenin sağındaki herhangi bir rota parametresi bir değere sahipse, işlem başarısız olur.
  * <!-- review default-valued parameters optional parameters --> Bitişik varsayılan değerli parametreler ve isteğe bağlı parametreler mümkün olduğunda daraltılır.

Rotanın bir kesimiyle eşleşmeyecek şekilde sağlanan değerler sorgu dizesine eklenir. Aşağıdaki tablo, rota şablonu `{controller}/{action}/{id?}`kullanırken sonucu gösterir.

| Ortam Değerleri                     | Açık Değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "Ana Sayfa"                | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | controller = "Order", action = "About" | `/Order/About`          |
| denetleyici = "Ev", renk = "Kırmızı" | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Rota değeri geçersiz liği ile ilgili sorunlar

Core 3.0ASP.NET itibariyle, daha önceki ASP.NET Core sürümlerinde kullanılan bazı URL oluşturma şemaları URL oluşturma yla iyi çalışmaz. ASP.NET Core ekibi, bu gereksinimleri gelecekteki bir sürümde gidermek için özellikler eklemeyi planlıyor. Şimdilik en iyi çözüm eski yönlendirme kullanmaktır.

Aşağıdaki kod, yönlendirme tarafından desteklenmeyen bir URL oluşturma düzeninin bir örneğini gösterir.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

Önceki kodda, `culture` rota parametresi yerelleştirme için kullanılır. Arzu, parametrenin `culture` her zaman ortam değeri olarak kabul edilmesidir. Ancak, `culture` parametre gerekli değerlerin çalışma şekli nedeniyle ortam değeri olarak kabul edilmez:

* Rota `"default"` `culture` şablonunda, rota parametresi sola, `controller`bu `controller` nedenle değişiklikler `culture`geçersiz kılmaz.
* Rota `"blog"` şablonunda, `culture` rota parametresi, gerekli değerlerde görünen `controller`, sağında olarak kabul edilir.

## <a name="configuring-endpoint-metadata"></a>Uç nokta meta verilerini yapılandırma

Aşağıdaki bağlantılar uç nokta meta verilerini yapılandırma hakkında bilgi sağlar:

* [Uç nokta yönlendirme ile Kors'u etkinleştirme](xref:security/cors#enable-cors-with-endpoint-routing)
* Özel `[MinimumAgeAuthorize]` bir öznitelik kullanarak [IAuthorizationPolicyProvider örneği](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [[Authorize] özniteliği ile kimlik doğrulaması test edin](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [[Authorize] özniteliği ile düzeni seçme](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [[Authorize] özniteliğini kullanarak ilkeler uygulama](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>RequireHost ile rotalarda eşleşen ana bilgisayar

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>belirtilen ana bilgisayar gerektiren rotaya bir kısıtlama uygular. Veya `RequireHost` [[Ana Bilgisayar]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parametresi şu olabilir:

* Ana `www.domain.com`bilgisayar: `www.domain.com` , herhangi bir bağlantı noktası ile eşleşir.
* Joker karakterli `*.domain.com`ana `www.domain.com`bilgisayar: , maçlar , `subdomain.domain.com`veya `www.subdomain.domain.com` herhangi bir bağlantı noktasında.
* Bağlantı `*:5000`noktası: , herhangi bir ana bilgisayar ile port 5000 eşleşir.
* Ana bilgisayar `www.domain.com:5000` ve `*.domain.com:5000`bağlantı noktası: veya, ev sahibi ve bağlantı noktası eşleşir.

Birden çok parametre `RequireHost` `[Host]`kullanılarak belirtilebilir veya . Kısıtlama, parametrelerden herhangi biri için geçerli olan ana bilgisayarları eşler. Örneğin, `[Host("domain.com", "*.domain.com")]` `domain.com`eşleşmeler `www.domain.com`, `subdomain.domain.com`ve .

Aşağıdaki kod, `RequireHost` rotada belirtilen ana bilgisayarı gerektiren kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Aşağıdaki kod, `[Host]` belirtilen ana bilgisayarlardan herhangi birini gerektirecek şekilde denetleyicideki özniteliği kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

`[Host]` Öznitelik hem denetleyiciye hem de eylem yöntemine uygulandığında:

* Eylemdeki öznitelik kullanılır.
* Denetleyici özniteliği yoksayılır.

## <a name="performance-guidance-for-routing"></a>Yönlendirme için performans kılavuzu

Yönlendirmenin çoğu, performansı artırmak için Core 3.0 ASP.NET güncelleştirildi.

Bir uygulamanın performans sorunları olduğunda, yönlendirmenin sorun olduğundan sık sık şüphelenilir. Yönlendirmenin şüphelenilmesinin nedeni, denetleyiciler ve Razor Pages gibi çerçevelerin günlük iletilerinde çerçeve içinde harcanan süreyi bildirmesidir. Denetleyiciler tarafından bildirilen süre ile isteğin toplam süresi arasında önemli bir fark olduğunda:

* Geliştiriciler sorunun kaynağı olarak uygulama kodlarını ortadan kaldırır.
* Yönlendirmenin neden olduğunu varsaymak yaygındır.

Yönlendirme, binlerce uç nokta kullanılarak performans testine tabi dir. Tipik bir uygulamanın çok büyük olarak bir performans sorunuyla karşılaşması olası değildir. Yavaş yönlendirme performansının en yaygın temel nedeni genellikle kötü davranan özel bir ara yazılımdır.

Aşağıdaki kod örneği, gecikme kaynağını daraltma için temel bir teknik gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Zaman yönlendirmesi için:

* Her ara yazılımı, önceki kodda gösterilen zamanlama ara yazılımının bir kopyasıyla birbirinden ayrılın.
* Zamanlama verilerini kodla ilişkilendirmek için benzersiz bir tanımlayıcı ekleyin.

Bu, `10ms`örneğin.  Ara yazılım `Time 2` `Time 1` içinde `UseRouting` harcanan zamanı raporlardan çıkarma.

Aşağıdaki kod, önceki zamanlama koduna daha kompakt bir yaklaşım kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Pahalı yönlendirme özellikleri

Aşağıdaki liste, temel rota şablonlarıyla karşılaştırıldığında nispeten pahalı olan yönlendirme özelliklerine ilişkin bazı bilgiler sağlar:

* Normal ifadeler: Karmaşık veya az miktarda girişle uzun süre çalışan normal ifadeler yazmak mümkündür.

* Karmaşık segmentler`{x}-{y}-{z}`( ): 
  * Normal bir URL yolu kesimini ayrıştırmaktan çok daha pahalıdır.
  * Çok daha fazla alt dizeleri tahsis neden.
  * Karmaşık segment mantığı, Core 3.0 yönlendirme performans güncelleştirmesi ASP.NET güncelleştirilemedi.

* Eşzamanlı veri erişimi: Birçok karmaşık uygulama yönlendirmelerinin bir parçası olarak veritabanıerişimine sahiptir. ASP.NET Core 2.2 ve önceki yönlendirme veritabanı erişim yönlendirmedesteklemek için doğru genişletilebilirlik noktaları sağlamayabilir. Örneğin, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>ve <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> senkron. Gibi ekstensibilite noktaları <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> ve <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> asynchronous vardır.

## <a name="guidance-for-library-authors"></a>Kütüphane yazarları için rehberlik

Bu bölümde, yönlendirmenin üzerine bina kitaplık yazarları için kılavuz içerir. Bu ayrıntılar, uygulama geliştiricilerin yönlendirmeyi genişleten kitaplıkları ve çerçeveleri kullanarak iyi bir deneyim yaşamalarını sağlamak için tasarlanmıştır.

### <a name="define-endpoints"></a>Uç noktaları tanımlama

URL eşlemi için yönlendirme kullanan bir çerçeve oluşturmak için, üstüne inşa edilen <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>bir kullanıcı deneyimi tanımlayarak başlayın.

**DO** üstüne inşa <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>. Bu, kullanıcıların diğer ASP.NET Core özellikleriyle birlikte çerçevenizi karışıklık olmadan oluşturmalarına olanak tanır. Her ASP.NET Core şablonu yönlendirme içerir. Yönlendirmenin kullanıcılar için mevcut ve tanıdık olduğunu varsayalım.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**Do** bu uygular bir `MapMyFramework(...)` çağrıdan mühürlü <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>bir beton türü döndürün. Çoğu `Map...` çerçeve yöntemi bu deseni izler. Arayüz: `IEndpointConventionBuilder`

* Meta verilerin birliğe uygunluğunu sağlar.
* Çeşitli uzatma yöntemleri ile hedeflenir.

Kendi türünüzü bildirmek, oluşturucuya kendi çerçeveye özgü işlevselliğinizi eklemenize olanak tanır. Bir çerçeve beyan oluşturucu sarın ve ona aramaları iletmek için ok.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**CONSIDER** Kendi <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>yazma düşünün . `EndpointDataSource`uç nokta koleksiyonunu bildirmek ve güncelleştirmek için düşük düzeyli ilkel dir. `EndpointDataSource`denetleyiciler ve Razor Pages tarafından kullanılan güçlü bir API'dir.

Yönlendirme testleri, güncelleştirilmeyen bir veri kaynağının temel bir [örneğine](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) sahiptir.

Varsayılan olarak bir `EndpointDataSource` kayıt girişiminde **BULUNMAYıN.** Kullanıcıların çerçevenizi <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>'de kaydettirmelerini zorunlu kılmaktadır. Yönlendirme felsefesi hiçbir şey varsayılan olarak dahil olmasıdır `UseEndpoints` ve bu uç noktaları kaydetmek için yerdir.

### <a name="creating-routing-integrated-middleware"></a>Yönlendirme entegreli ara yazılım oluşturma

Meta veri türlerini arabirim olarak tanımlamayı **düşünün.**

**DO,** sınıflar ve yöntemler üzerinde bir öznitelik olarak meta veri türlerini kullanmayı mümkün kılar.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Denetleyiciler ve Razor Pages gibi çerçeveler, meta veri özniteliklerini türleri ve yöntemleri uygulayarak destekler. Meta veri türlerini bildirirseniz:

* Onları öznitelik olarak erişilebilir hale [getirin.](/dotnet/csharp/programming-guide/concepts/attributes/)
* Çoğu kullanıcı öznitelikleri uygulama konusunda bilgindir.

Bir meta veri türünü arabirim olarak bildirmek başka bir esneklik katmanı ekler:

* Arayüzler birleştirilebilir.
* Geliştiriciler, birden çok ilkeyi birleştiren kendi türlerini bildirebilir.

**DO,** aşağıdaki örnekte gösterildiği gibi meta verileri geçersiz kılmayı mümkün kılar:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Bu yönergeleri izlemenin en iyi yolu **işaretçi meta verileri**tanımlamaktan kaçınmaktır:

* Yalnızca bir meta veri türünün varlığını aramayın.
* Meta verilerde bir özellik tanımlayın ve özelliği denetleyin.

Meta veri toplama sıralanır ve öncelik tarafından geçersiz kılmayı destekler. Denetleyiciler söz konusu olduğunda, eylem yöntemine ilişkin meta veriler en belirgindir.

**Ara** ware ile ve yönlendirme olmadan yararlı olun.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

Bu kılavuza bir örnek olarak, `UseAuthorization` ara yazılımı göz önünde bulundurun. Yetkilendirme ara ware bir geri dönüş ilkesi geçmek için izin verir. <!-- shown where?  (shown here) --> Geri dönüş ilkesi, belirtildiği takdirde, her ikisi için de geçerlidir:

* Belirtilmeyen bir ilke olmadan uç noktalar.
* Bitiş noktasıyla eşleşmeyen istekler.

Bu, yetkilendirme ara ware yönlendirme bağlamı dışında yararlı hale getirir. Yetkilendirme middleware geleneksel middleware programlama için kullanılabilir.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Yönlendirme, istek URI'lerini uç noktalara eşlemekten ve gelen istekleri bu uç noktalara göndermekten sorumludur. Rotalar uygulamada tanımlanır ve uygulama başladığında yapılandırılır. Bir rota isteğe bağlı olarak istekte bulunan URL'den değerleri ayıklayabilir ve bu değerler daha sonra istek işleme için kullanılabilir. Uygulamadaki rota bilgilerini kullanarak yönlendirme, uç noktalara eşlenen URL'ler de oluşturabilir.

Core 2.2ASP.NET en son yönlendirme senaryolarını kullanmak için, Aşağıdaki lerde MVC `Startup.ConfigureServices`hizmetleri kaydına uyumluluk [sürümünü](xref:mvc/compatibility-version) belirtin:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Seçenek, <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> yönlendirmenin uç nokta tabanlı mantığı mı yoksa ASP.NET <xref:Microsoft.AspNetCore.Routing.IRouter>Core 2.1 veya daha önceki ASP.NET tabanlı mantığını içsel olarak kullanıp kullanmaması gerektiğini belirler. Uyumluluk sürümü 2.2 veya daha sonra olarak ayarlandığında, varsayılan değer `true`. Önceki yönlendirme `false` mantığını kullanmak için değeri ayarlayın:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Tabanlı yönlendirme <xref:Microsoft.AspNetCore.Routing.IRouter>hakkında daha fazla bilgi [için, bu konunun ASP.NET Core 2.1 sürümüne](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)bakın.

> [!IMPORTANT]
> Bu belge, düşük düzeyli ASP.NET Çekirdek yönlendirmeyi kapsar. ASP.NET Core MVC yönlendirmesi hakkında <xref:mvc/controllers/routing>bilgi için bkz. Razor Pages'deki yönlendirme kuralları hakkında daha <xref:razor-pages/razor-pages-conventions>fazla bilgi için bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Yönlendirme temelleri

Çoğu uygulama, URL'lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel `{controller=Home}/{action=Index}/{id?}`rota:

* Temel ve açıklayıcı yönlendirme düzenini destekler.
* UI tabanlı uygulamalar için yararlı bir başlangıç noktasıdır.

Geliştiriciler genellikle [öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing) veya özel geleneksel rotalar kullanarak özel durumlarda bir uygulamanın yüksek trafik alanlarına ek kısa yollar ekleyin. Özel durumlar örnekleri, blog ve e-ticaret uç noktaları içerir.

Web API'leri, uygulamanın işlevselliğini işlemlerin HTTP fiilleri tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır. Bu, aynı mantıksal kaynak üzerinde, get ve POST gibi birçok işlemin aynı URL'yi kullandığı anlamına gelir. Öznitelik yönlendirme, bir API'nin genel uç nokta düzenini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Razor Pages uygulamaları, bir uygulamanın *Sayfalar* klasöründe adlandırılmış kaynaklara hizmet etmek için varsayılan geleneksel yönlendirmeyi kullanır. Razor Pages yönlendirme davranışını özelleştirmenize olanak tanıyan ek kurallar mevcuttur. Daha fazla bilgi için <xref:razor-pages/index> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

URL oluşturma desteği, uygulamanın sıkı kodlama URL'leri olmadan geliştirilen uygulamanın birbirine bağlanmasını sağlar. Bu destek, temel bir yönlendirme yapılandırmasıyla başlayıp uygulamanın kaynak düzeni belirlendikten sonra rotaların değiştirilmesine olanak tanır.

Yönlendirme, bir uygulamadaki`Endpoint`mantıksal uç noktaları temsil etmek için uç *noktaları* ( ) kullanır.

Bitiş noktası, istekleri işlemek için bir temsilci ve rasgele meta veri koleksiyonu tanımlar. Meta veriler, her bitiş noktasına bağlı ilkelere ve yapılandırmaya dayalı çapraz kesme endişelerini uygulamak için kullanılır.

Yönlendirme sistemi aşağıdaki özelliklere sahiptir:

* Rota şablonu sözdizimi, tokenize rota parametreleri ile yolları tanımlamak için kullanılır.
* Geleneksel stil ve öznitelik tarzı uç nokta yapılandırması izin verilir.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>URL parametrenin belirli bir bitiş noktası kısıtlaması için geçerli bir değer bulunup içermediğini belirlemek için kullanılır.
* MVC/Razor Pages gibi uygulama modelleri, yönlendirme senaryolarının öngörülebilir bir uygulamasına sahip olan tüm uç noktalarını kaydeder.
* Yönlendirme uygulaması, ara yazılım boru hattında istenilen her yerde yönlendirme kararları verir.
* Bir Yönlendirme Middleware sonra görünen Middleware belirli bir istek URI için Yönlendirme Middleware's bitiş noktası kararının sonucunu inceleyebilirsiniz.
* Uygulamadaki tüm uç noktaları ara yazılım boru hattının herhangi bir yerinde sayısalhale getirmek mümkündür.
* Bir uygulama, uç nokta bilgilerine dayalı URL'ler (örneğin, yeniden yönlendirme veya bağlantılar için) oluşturmak için yönlendirmeyi kullanabilir ve böylece sabit kodlanmış URL'lerden kaçınabilir ve bu da korunmaya yardımcı olur.
* URL oluşturma rasgele genişletilebilirlik destekleyen adreslere dayanır:

  * Bağlantı Jeneratörü API ' si (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) URL'leri oluşturmak için bağımlılık [enjeksiyonu (DI)](xref:fundamentals/dependency-injection) kullanılarak her yerde çözülebilir.
  * Bağlantı Jeneratörü API'sinin DI üzerinden <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> kullanılamadığı durumlarda, URL'ler oluşturmak için yöntemler sunar.

> [!NOTE]
> Core 2.2'ASP.NET uç nokta yönlendirmesinin serbest bırakılması ile, uç nokta bağlama MVC/Razor Pages eylemleri ve sayfaları ile sınırlıdır. Uç nokta bağlama yeteneklerinin genişletmeleri gelecekteki sürümler için planlanır.

Yönlendirme, sınıf tarafından [ara yazılım](xref:fundamentals/middleware/index) ardışık hattına <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> bağlanır. [ASP.NET Core MVC,](xref:mvc/overview) yapılandırmasının bir parçası olarak ara yazılım boru hattına yönlendirme ekler ve MVC ve Razor Pages uygulamalarında yönlendirmeyi işler. Yönlendirmeyi bağımsız bir bileşen olarak nasıl kullanacağınızı öğrenmek için Yönlendirme [Ara ware'i Kullan](#use-routing-middleware) bölümüne bakın.

### <a name="url-matching"></a>URL eşleştirme

URL eşleştirme, yönlendirmenin gelen isteği *bitiş noktasına*gönderme işlemidir. Bu işlem URL yolundaki verilere dayanır, ancak istekteki verileri dikkate almak için genişletilebilir. İstekleri ayrı işleyicilere gönderme özelliği, bir uygulamanın boyutunu ve karmaşıklığını ölçeklemenin anahtarıdır.

Uç nokta yönlendirmedeki yönlendirme sistemi tüm sevk kararlarından sorumludur. Ara yazılım, seçili bitiş noktasına dayalı ilkeler uyguladığından, yönlendirme sistemi içinde gönderme veya güvenlik ilkelerinin uygulanmasını etkileyebilecek herhangi bir kararın verliği etkilemesi önemlidir.

Bitiş noktası temsilcisi yürütüldüğünde, [RouteContext.RouteData'nın](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özellikleri, şimdiye kadar gerçekleştirilen istek işlemedayalı uygun değerlere ayarlanır.

[RouteData.Values,](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) rotadan üretilen *rota değerlerinin* sözlüğüdür. Bu değerler genellikle URL'yi belirterek belirlenir ve kullanıcı girişini kabul etmek veya uygulama içinde daha fazla gönderme kararı almak için kullanılabilir.

[RouteData.DataTokens,](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) eşleşen rotayla ilgili ek verilerin yer alan bir özellik torbasıdır. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>uygulamanın hangi rotayla eşleştiğine göre karar verebilmeleri için durum verilerinin her rotayla ilişkiye alinmasını desteklemek için sayılmısa. Bu değerler geliştirici tarafından tanımlanır ve yönlendirme davranışını hiçbir şekilde **etkilemez.** Ayrıca, [RouteData.DataTokens'de](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) saklanan değerler, string'lere dönüştürülebilen [RouteData.Values'in](xref:Microsoft.AspNetCore.Routing.RouteData.Values)aksine her türden olabilir.

[RouteData.Routers,](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) isteğin başarıyla eşleşmesinde yer alan yolların listesidir. Rotalar birbirinin içine iç içe olabilir. Özellik, <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşmeyle sonuçlanan mantıksal rota lar ağacından geçen yolu yansıtır. Genellikle, ilk öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> rota koleksiyonudur ve URL oluşturma için kullanılmalıdır. Son öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşen rota işleyicisi.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>LinkGenerator ile URL üretimi

URL oluşturma, yönlendirmenin bir rota değerleri kümesine dayalı bir URL yolu oluşturabileceği işlemdir. Bu, uç noktalarınızla bunlara erişen URL'ler arasında mantıksal bir ayrım yapılmasına olanak tanır.

Endpoint yönlendirme Bağlantı Jeneratör API<xref:Microsoft.AspNetCore.Routing.LinkGenerator>içerir ( ). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>[DI'den](xref:fundamentals/dependency-injection)alınabilen tek tonlu bir hizmettir. API, yürütme isteği bağlamının dışında kullanılabilir. MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ve senaryolar <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Yardımcıları ve [Eylem Sonuçları](xref:mvc/controllers/actions)gibi , bağlantı oluşturma yetenekleri sağlamak için bağlantı jeneratör kullanın.

Bağlantı jeneratörü bir *adres* ve adres *şemaları*kavramı tarafından desteklenen. Adres düzeni, bağlantı oluşturma için göz önünde bulundurulması gereken uç noktaları belirlemenin bir yoludur. Örneğin, birçok kullanıcının MVC/Razor Sayfalarından aşina olduğu rota adı ve rota değerleri senaryoları adres düzeni olarak uygulanır.

Bağlantı jeneratörü aşağıdaki uzantı yöntemleri ile MVC/Razor Pages eylemlerine ve sayfalarına bağlantı verebilir:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Bu yöntemlerin aşırı yüklenmesi, `HttpContext`.. Bu yöntemler işlevsel olarak `Url.Action` `Url.Page` eşdeğerdir ve ancak ek esneklik ve seçenekler sunar.

Yöntemler `GetPath*` en çok `Url.Action` benzer `Url.Page` ve mutlak bir yol içeren bir URI oluşturmak. Yöntemler `GetUri*` her zaman bir şema ve ana bilgisayar içeren mutlak bir URI oluşturur. Yürütme isteği bağlamında `HttpContext` bir URI oluşturmayı kabul eden yöntemler. Ortam rotası değerleri, URL temel yolu, düzen ve yürütme isteğinden ana bilgisayar geçersiz kılınmadığı sürece kullanılır.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>bir adresle çağrılır. URI oluşturma iki adımda gerçekleşir:

1. Adres, adresle eşleşen uç noktaların listesine bağlıdır.
1. Her uç nokta, `RoutePattern` sağlanan değerlerle eşleşen bir rota deseni bulunana kadar değerlendirilir. Elde edilen çıkış, bağlantı jeneratörüne verilen diğer URI parçalarıyla birleştirilir ve döndürülür.

Her tür <xref:Microsoft.AspNetCore.Routing.LinkGenerator> adres için standart bağlantı oluşturma yeteneklerini destekleyerek sağlanan yöntemler. Bağlantı jeneratörü kullanmak için en uygun yolu belirli bir adres türü için işlemleri gerçekleştirmek uzantısı yöntemleri geçer.

| Uzatma Yöntemi   | Açıklama                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Sağlanan değerlere dayalı mutlak bir yol ile bir URI oluşturur. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Sağlanan değerlere dayalı mutlak bir URI oluşturur.             |

> [!WARNING]
> Arama <xref:Microsoft.AspNetCore.Routing.LinkGenerator> yöntemlerinin aşağıdaki etkilerine dikkat edin:
>
> * Gelen `GetUri*` isteklerin üstbilgisini `Host` doğrulamayan bir uygulama yapılandırmasında uzantı yöntemlerini dikkatli kullanın. Gelen `Host` isteklerin üstbilgisi doğrulanmazsa, güvenilmeyen istek girişi bir görünüm/sayfa olarak URI'deki istemciye geri gönderilebilir. Tüm üretim uygulamalarının üstbilgiyi `Host` bilinen geçerli değerlere göre doğrulamak için sunucularını yapılandırmalarını öneririz.
>
> * Middleware'de dikkatli <xref:Microsoft.AspNetCore.Routing.LinkGenerator> kullanın `Map` veya `MapWhen`. `Map*`bağlantı oluşturma çıktısını etkileyen yürütme isteğinin temel yolunu değiştirir. Tüm API'ler <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bir temel yol belirtmeye izin verir. Bağlantı oluşturma üzerindeki etkisini geri `Map*`almak için her zaman boş bir temel yol belirtin.

## <a name="differences-from-earlier-versions-of-routing"></a>Yönlendirmenin önceki sürümlerinden farklar

core 2.2 veya daha sonraki ASP.NET ve ASP.NET Core yönlendirme önceki sürümlerinde bitiş noktası yönlendirme arasında birkaç fark vardır:

* Uç nokta yönlendirme sistemi, 'den <xref:Microsoft.AspNetCore.Routing.IRouter> <xref:Microsoft.AspNetCore.Routing.Route>devralma dahil olmak üzere tabanlı genişletilebilirliği desteklemez.

* Endpoint yönlendirme [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)desteklemez. Uyumluluk şim'ini kullanmaya`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`devam etmek için 2.1 uyumluluk [sürümünü](xref:mvc/compatibility-version) () kullanın.

* Endpoint Yönlendirme, geleneksel yolları kullanırken oluşturulan ÜR'lerin kasası için farklı davranışlara sahiptir.

  Aşağıdaki varsayılan rota şablonunu göz önünde bulundurun:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Aşağıdaki yolu kullanarak bir eyleme bağlantı oluşturduğunuzu varsayalım:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  -tabanlı <xref:Microsoft.AspNetCore.Routing.IRouter>yönlendirme ile, bu kod, sağlanan `/blog/ReadPost/17`rota değerinin kasası saygı bir URI oluşturur. Core 2.2 veya daha sonra ASP.NET'da uç nokta yönlendirmesi ("Blog" büyük harfle) üretir. `/Blog/ReadPost/17` Uç nokta yönlendirme, `IOutboundParameterTransformer` bu davranışı genel olarak özelleştirmek veya URL'leri eşleme için farklı kurallar uygulamak için kullanılabilecek arabirimi sağlar.

  Daha fazla bilgi için [Parametre transformatörü başvuru](#parameter-transformer-reference) bölümüne bakın.

* MVC/Razor Pages tarafından geleneksel rotalara sahip kullanılan Bağlantı Oluşturma, var olmayan bir denetleyiciye/eyleme veya sayfaya bağlanmaya çalışırken farklı şekilde çalışır.

  Aşağıdaki varsayılan rota şablonunu göz önünde bulundurun:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Varsayılan şablonu kullanarak bir eyleme bağlantı oluşturduğunuzu varsayalım:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  -tabanlı `IRouter`yönlendirme ile, sonuç her `/Blog/ReadPost/17`zaman , `BlogController` var olmasa veya bir `ReadPost` eylem yöntemi olmasa bile. Beklendiği gibi, ASP.NET Core 2.2 veya daha `/Blog/ReadPost/17` sonra uç nokta yönlendirme eylem yöntemi varsa üretir. *Ancak, eylem yoksa bitiş noktası yönlendirme boş bir dize üretir.* Kavramsal olarak, uç nokta yönlendirme, eylem yoksa bitiş noktasının var olduğunu varsaymaz.

* Bağlantı oluşturma *ortam değeri geçersiz leştirme algoritması,* uç nokta yönlendirmeile kullanıldığında farklı şekilde çalışır.

  *Ortam değeri geçersiz liği,* şu anda yürütülen istekten (ortam değerleri) hangi rota değerlerinin bağlantı oluşturma işlemlerinde kullanılabileceğini belirleyen algoritmadır. Geleneksel yönlendirme, farklı bir eyleme bağlantı verirken her zaman geçersiz kılınan ek rota değerlerini. Öznitelik yönlendirmesi, ASP.NET Core 2.2 yayımlanmasından önce bu davranışa sahip değildi. ASP.NET Core'un önceki sürümlerinde, aynı rota parametre adlarını kullanan başka bir eyleme bağlantılar bağlantı oluşturma hatalarıyla sonuçlanmıştır. Core 2.2 veya daha sonraki ASP.NET, yönlendirmenin her iki biçimi de başka bir eyleme bağlantı verirken değerleri geçersiz kılın.

  Core 2.1 veya daha önce ASP.NET aşağıdaki örneği göz önünde bulundurun. Başka bir eyleme (veya başka bir sayfaya) bağlantı verirken, rota değerleri istenmeyen yollarla yeniden kullanılabilir.

  */Pages/Store/Product.cshtml*olarak :

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  In */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  URI Core `/Store/Product/18` 2.1 veya daha önce ASP.NET ise, Store/Info `@Url.Page("/Login")` sayfasında `/Login/18`oluşturulan bağlantı . Bağlantı `id` hedefi tamamen uygulamanın farklı bir parçası olsa da, 18 değeri yeniden kullanılır. `/Login` Sayfa bağlamında `id` rota değeri büyük olasılıkla bir kullanıcı kimliği değeridir, mağaza ürün kimliği değeri değil.

  Core 2.2 veya daha sonra ASP.NET ile bitiş `/Login`noktası yönlendirmesinde, sonuç . Ortam değerleri, bağlı hedef farklı bir eylem veya sayfa olduğunda yeniden kullanılmaz.

* Yuvarlama rota parametresi sözdizimi: Çift yıldız işareti (`**`) catch-all parametre sözdizimi kullanırken iletme çizgileri kodlanır.

  Bağlantı oluşturma sırasında yönlendirme sistemi, ileri eğik çizgiler dışında çift`**`yıldız () catch-all parametresi (örneğin, `{**myparametername}`) yakalanan değeri kodlar. Çift yıldız lı catch-all core 2.2 veya daha sonra ASP.NET tabanlı yönlendirme ile `IRouter`desteklenir.

  ASP.NET Core () önceki`{*myparametername}`sürümlerinde tek yıldız catch-all parametre sözdizimi desteklenmeye devam kalır ve ileri eğik çizgiler kodlanır.

  | Yol              | Bağlantı ile oluşturulan<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`(ön eğik çizgi kodlanır)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Middleware örneği

Aşağıdaki örnekte, ara yazılım, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> depo ürünlerini listeleyen bir eylem yöntemine bağlantı oluşturmak için API'yi kullanır. Bir sınıfa enjekte ederek bağlantı jeneratörü `GenerateLink` kullanarak ve arama bir uygulama herhangi bir sınıf için kullanılabilir.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Yolları oluşturma

Çoğu uygulama arayarak <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> veya üzerinde <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>tanımlanan benzer uzantı yöntemlerinden birini arayarak rotalar oluşturur. Uzantı <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> yöntemlerinden herhangi biri <xref:Microsoft.AspNetCore.Routing.Route> bir örnek oluşturur ve rota koleksiyonuna ekler.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>rota işleyicisi parametrekabul etmez. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>yalnızca <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. MVC'de yönlendirme hakkında daha fazla <xref:mvc/controllers/routing>bilgi edinmek için bkz.

Aşağıdaki kod örneği, tipik <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> bir ASP.NET Core MVC rota tanımı tarafından kullanılan bir çağrı örneğidir:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Bu şablon bir URL yolu yla eşleşir ve rota değerlerini ayıklar. Örneğin, yol `/Products/Details/17` aşağıdaki rota değerlerini oluşturur: `{ controller = Products, action = Details, id = 17 }`.

Rota değerleri, URL yolunu n için bölümlere bölerek ve her kesimin rota şablonundaki *rota parametre* adı ile eşleştirilerek belirlenir. Rota parametreleri adlandırılır. Parametre adı ayraçlara `{ ... }`ekleyerek tanımlanan parametreler.

Önceki şablon da URL yolu `/` eşleşebilir `{ controller = Home, action = Index }`ve değerleri üretmek. Bu, ve `{controller}` `{action}` rota parametrelerinin varsayılan `id` değerlere sahip olması ve rota parametresi isteğe bağlı olması nedeniyle oluşur. Eşitler işareti`=`( ) ardından rota parametresi adı parametre için varsayılan bir değer tanımlar. Rota parametresi adı sonra bir soru işareti (`?`) isteğe bağlı bir parametre tanımlar.

Varsayılan değeri olan rota parametreleri, rota eşleştiğinde *her zaman* bir rota değeri üretir. İlgili URL yolu kesimi yoksa, isteğe bağlı parametreler rota değeri oluşturmaz. Rota [şablonu](#route-template-reference) senaryoları ve sözdiziminin ayrıntılı bir açıklaması için Rota şablonu başvuru bölümüne bakın.

Aşağıdaki örnekte, rota parametre `{id:int}` tanımı rota parametresi için bir [rota kısıtlaması](#route-constraint-reference) `id` tanımlar:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Bu şablon, gibi `/Products/Details/17` bir `/Products/Details/Apples`URL yolu eşleşir ama değil. Rota kısıtlamaları, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> bunları doğrulamak için rota değerlerini uygular ve inceler. Bu örnekte, rota `id` değerinin bir tamsayıya dönüştürülebilir olması gerekir. Çerçeve tarafından sağlanan rota kısıtlamalarının açıklaması için [rota kısıtlaması-başvuruya](#route-constraint-reference) bakın.

' `dataTokens`ve `defaults` <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> . için `constraints`ek kabul değerleri Bu parametrelerin tipik kullanımı, anonim tür eşleme rota parametre adlarının özellik adlarının bulunduğu anonim olarak yazılan bir nesneyi geçmektir.

Aşağıdaki <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> örnekler eşdeğer yollar oluşturur:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Kısıtlamaları ve varsayılanları tanımlamak için satır satır ekindizi basit yollar için uygun olabilir. Ancak, satır arasözcü tarafından desteklenmeyen veri belirteçleri gibi senaryolar da vardır.

Aşağıdaki örnekte birkaç ek senaryo gösterilmiş:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Önceki şablon gibi `/Blog/All-About-Routing/Introduction` bir URL yolu eşleşir `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`ve değerleri ayıklar. Şablonda karşılık `controller` gelen `action` rota parametreleri olmamasına rağmen, varsayılan rota değerleri için ve rota tarafından üretilir. Varsayılan değerler rota şablonunda belirtilebilir. `article` Rota parametresi, rota parametresi adından önce çift yıldız`**`() görünümüyle *tümünü yakalama* olarak tanımlanır. Tümyolu yakalama parametreleri URL yolunun geri kalanını yakalar ve boş dizeyle eşleşebilir.

Aşağıdaki örnek, rota kısıtlamaları ve veri belirteçleri ekler:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Önceki şablon gibi `/en-US/Products/5` bir URL yolu eşleşir `{ controller = Products, action = Details, id = 5 }` ve değerleri ve `{ locale = en-US }`veri belirteçleri ayıklar.

![Yerel Windows belirteçleri](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Rota sınıfı URL oluşturma

Sınıf, <xref:Microsoft.AspNetCore.Routing.Route> rota şablonuyla bir dizi rota değerini birleştirerek URL oluşturma yı da gerçekleştirebilir. Bu mantıksal olarak URL yolunu eşleştirme nin ters işlemidir.

> [!TIP]
> URL oluşturmayı daha iyi anlamak için, hangi URL'yi oluşturmak istediğinizi hayal edin ve ardından bir rota şablonunun bu URL ile nasıl eşleşeceğini düşünün. Hangi değerler üretilecek? Bu, URL oluşturmanın <xref:Microsoft.AspNetCore.Routing.Route> sınıfta nasıl çalıştığının kaba karşılığıdır.

Aşağıdaki örnekte genel bir ASP.NET Core MVC varsayılan rota kullanır:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Rota değerleri `{ controller = Products, action = List }`yle URL `/Products/List` oluşturulur. Rota değerleri, URL yolunu oluşturmak için ilgili rota parametreleri için ikame edilir. İsteğe bağlı bir rota parametresi olduğundan, `id` URL `id`için bir değer olmadan başarıyla oluşturulur.

Rota değerleri `{ controller = Home, action = Index }`yle URL `/` oluşturulur. Sağlanan rota değerleri varsayılan değerlerle eşleşir ve varsayılan değerlere karşılık gelen segmentler güvenli bir şekilde atlanır.

Her iki URL de aşağıdaki rota tanımıyla`/Home/Index` `/`(ve) URL oluşturmak için kullanılan aynı rota değerlerini kullanarak gidiş-dönüş oluşturdu.

> [!NOTE]
> Core MVC'ASP.NET kullanan <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> bir uygulama, doğrudan yönlendirmeye çağırmak yerine URL'ler oluşturmak için kullanmalıdır.

URL oluşturma hakkında daha fazla bilgi için [Url oluşturma başvuru](#url-generation-reference) bölümüne bakın.

## <a name="use-routing-middleware"></a>Yönlendirme Middleware'i kullanma

Uygulamanın proje dosyasındaki [Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun.

Aşağıdaki durumlarda servis konteynerine `Startup.ConfigureServices`yönlendirme ekleme:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Rotalar `Startup.Configure` yöntemde yapılandırılmalıdır. Örnek uygulama aşağıdaki API'leri kullanır:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Yalnızca HTTP GET istekleriyle eşleşir.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Aşağıdaki tablo, verilen ÜRB'lerle yanıtları gösterir.

| URI                    | Yanıt                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Merhaba! Rota değerleri: [işlem, oluşturma], [id, 3] |
| `/package/track/-3`    | Merhaba! Rota değerleri: [işlem, parça], [id, -3] |
| `/package/track/-3/`   | Merhaba! Rota değerleri: [işlem, parça], [id, -3] |
| `/package/track/`      | İstek suya düşüyor, eşleşme yok.              |
| `GET /hello/Joe`       | Merhaba Joe!                                          |
| `POST /hello/Joe`      | İstek ile düşer, maçlar HTTP GET sadece. |
| `GET /hello/Joe/Smith` | İstek suya düşüyor, eşleşme yok.              |

Çerçeve, rotalar oluşturmak için bir<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>dizi uzatma yöntemi sağlar ( ):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Yöntemler, `Map[Verb]` yöntem adında http fiiliile yolu sınırlamak için kısıtlamalar kullanır. Örneğin, bkz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>ve .

## <a name="route-template-reference"></a>Rota şablonu başvurusu

Kıvırcık ayraçlar içindeki`{ ... }`belirteçler ( ) rota eşleştiğinde bağlanan *rota parametrelerini* tanımlar. Bir rota segmentinde birden fazla rota parametresi tanımlayabilirsiniz, ancak bunların gerçek bir değerle ayrılması gerekir. Örneğin, `{controller=Home}{action=Index}` geçerli bir yol değildir, çünkü ile `{controller}` . `{action}` Bu rota parametrelerinin bir adı olmalıdır ve ek öznitelikler belirtilmiş olabilir.

Rota parametreleri dışındaki gerçek metin `{id}`(örneğin, ) `/` ve yol ayırıcısı URL'deki metyle eşleşmelidir. Metin eşleştirme büyük/küçük harf duyarsız ve URL'ler yolunun deşifre gösterimine dayanır. Bir literal rota parametresi`{` delimiter `}``{{` (veya) eşleştirmek için , `}}`karakter (veya ) tekrarlayarak delimiter kaçış.

İsteğe bağlı dosya uzantısı ile bir dosya adını yakalamaya çalışan URL desenleri ek hususlar vardır. Örneğin, şablonu `files/{filename}.{ext?}`göz önünde bulundurun. Her ikisi `filename` için `ext` değerler ve var olduğunda, her iki değer de doldurulur. URL'de yalnızca `filename` bir değer varsa, rota eşleşir,`.`çünkü son daki dönem ( ) isteğe bağlıdır. Aşağıdaki URL'ler bu rotayla eşleşir:

* `/files/myFile.txt`
* `/files/myFile`

Uri'nin geri kalanına`*`bağlamak için rota`**`parametresine bir önek olarak yıldız işareti () veya çift yıldız ( ) kullanabilirsiniz. Bunlara *tümlerini yakalama* parametreleri denir. Örneğin, `blog/{**slug}` `slug` rota değerine atanan `/blog` uri ile başlayan ve onu izleyen herhangi bir değere sahip herhangi bir URI ile eşleşir. Tümlerini yakalama parametreleri boş dizeyle de eşleşebilir.

Tümlerini yakalama parametresi, yol ayırıcısı (`/`) karakterleri de dahil olmak üzere bir URL oluşturmak için kullanıldığında uygun karakterlerden kaçar. Örneğin, rota `foo/{*path}` değerleri `{ path = "my/path" }` ile rota `foo/my%2Fpath`oluşturur. Kaçan ileri eğik çizgiye dikkat edin. Gidiş-dönüş yol ayırıcı karakterleri için `**` rota parametresi önekini kullanın. Üreten `foo/{**path}` `{ path = "my/path" }` `foo/my/path`rota.

Rota parametreleri, eşitler işaretiyle ayrılan parametre adı ()`=`ile ayrıldıktan sonra varsayılan değeri belirterek varsayılan *değerlere* sahip olabilir. Örneğin, `{controller=Home}` varsayılan `Home` değer olarak `controller`tanımlar. Parametrenin URL'sinde değer yoksa varsayılan değer kullanılır. Rota parametreleri, parametre adının sonuna`?`bir soru işareti ( ) ekleyerek `id?`isteğe bağlı hale getirilir. İsteğe bağlı değerler ve varsayılan rota parametreleri arasındaki fark, varsayılan&mdash;değeri olan bir rota parametresinin her zaman isteğe bağlı bir parametrenin yalnızca istek URL'si tarafından bir değer sağlandığında bir değere sahip olduğu bir değer üretmesidir.

Rota parametrelerinde URL'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamalar olabilir. Rota parametre`:`adı sonra bir iki nokta üst üste ( ) ve kısıtlama adı ekleme bir rota parametresi üzerinde *satır içinde bir kısıtlama* belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, kısıtlama adından`(...)`sonra parantez içinde ( ) ekolarak konum. Birden çok satır içi kısıtlamalar başka bir`:`üst üste ( ) ve kısıtlama adı ekleyerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, URL işlemede kullanılacak bir örnek oluşturmak için <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> hizmete aktarılır. Örneğin, rota şablonu `blog/{article:minlength(10)}` bağımsız `minlength` değişkenile `10`bir kısıtlama belirtir. Rota kısıtlamaları ve çerçeve tarafından sağlanan kısıtlamaların listesi hakkında daha fazla bilgi için [Rota kısıtlaması başvuru](#route-constraint-reference) bölümüne bakın.

Rota parametreleri, bağlantılar oluştururken ve eylemleri ve sayfaları URL'lere eşleştirirken parametre değerini dönüştüren parametre transformatörlerine de sahip olabilir. Kısıtlamalar gibi, parametre transformatörleri de rota parametresi adından`:`sonra bir nokta üst üste ( ) ve transformatör adı eklenerek rota parametresine satır satır eklenebilir. Örneğin, rota şablonu `blog/{article:slugify}` bir `slugify` transformatör belirtir. Parametre transformatörleri hakkında daha fazla bilgi için [Parametre transformatör referans](#parameter-transformer-reference) bölümüne bakın.

Aşağıdaki tabloörnek rota şablonlarını ve davranışlarını gösterir.

| Rota Şablonu                           | Örnek Eşleşen URI    | İstek URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Sadece tek yol `/hello`eşleşir.                                     |
| `{Page=Home}`                            | `/`                     | Maçlar ve `Page` `Home`ayarlar .                                         |
| `{Page=Home}`                            | `/Contact`              | Maçlar ve `Page` `Contact`ayarlar .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` Denetleyici ve `List` eylem haritaları.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` Denetleyici ve `Details` eylem haritaları (123`id` olarak ayarlayın). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` Denetleyici ve `Index` yönteme haritalar (yoksayılır).`id`        |

Şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar da rota şablonu dışında belirtilebilir.

> [!TIP]
> Yerleşik yönlendirme uygulamalarının <xref:Microsoft.AspNetCore.Routing.Route>istekleri nasıl eşleştirbildiğini görmek için [Günlüğe Kaydetme'yi](xref:fundamentals/logging/index) etkinleştirin.

## <a name="reserved-routing-names"></a>Ayrılmış yönlendirme adları

Aşağıdaki anahtar kelimeler ayrılmış adlardır ve rota adları veya parametrelerolarak kullanılamaz:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Rota kısıtlaması başvurusu

Gelen URL'de bir eşleşme oluştuğunda ve URL yolu rota değerlerine belirtildiğinde rota kısıtlamaları yürütülür. Rota kısıtlamaları genellikle rota şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda evet/hayır kararı verir. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilemeyeceğini değerlendirmek için rota değerinin dışındaki verileri kullanır. Örneğin, http <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> fiiline dayalı bir isteği kabul edebilir veya reddedebilir. Kısıtlamalar yönlendirme istekleri ve bağlantı oluşturma kullanılır.

> [!WARNING]
> **Giriş doğrulama**için kısıtlamalar kullanmayın. **Giriş doğrulaması**için kısıtlamalar kullanılıyorsa, geçersiz giriş sonuçları *404 - 400* yerine Bulunamadı yanıt - Uygun bir hata iletisi ile *Hatalı İstek.* Rota kısıtlamaları, belirli bir rotanın girdilerini doğrulamak için değil, benzer yolları **ayrıştırmak** için kullanılır.

Aşağıdaki tablo, örnek rota kısıtlamaları ve bunların beklenen davranışlarını gösterir.

| kısıtlama | Örnek | Örnek Eşleşmeler | Notlar |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir sonayla eşleşir. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Kibrit `true` ler ya da 'yanlış. Duyarlı. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Değişmez kültürdeki geçerli `DateTime` bir değerle eşleşir. Önceki uyarıya bakın.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Değişmez kültürdeki geçerli `decimal` bir değerle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `double` bir değerle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `float` bir değerle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Geçerli `Guid` bir değerle eşleşir. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli `long` bir değerle eşleşir. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String en az 4 karakter olmalıdır. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String en fazla 8 karakter vardır. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String tam olarak 12 karakter uzunluğunda olmalıdır. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Dize en az 8 olmalıdır ve en fazla 16 karaktervardır. |
| `min(value)` | `{age:min(18)}` | `19` | İntesayı değeri en az 18 olmalıdır. |
| `max(value)` | `{age:max(120)}` | `91` | Tümsek değeri en fazla 120' dir. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | İntesayı değeri en az 18 ve en fazla 120 olmalıdır. |
| `alpha` | `{name:alpha}` | `Rick` | String bir veya daha fazla alfabetik karakterden `a` - `z`oluşmalıdır.  Duyarlı. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String normal ifadeyle eşleşmelidir. Normal bir ifade tanımlama yla ilgili ipuçlarına bakın. |
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre olmayan bir değerin mevcut olduğunu zorlamak için kullanılır. |

Birden çok, kolon-delimited kısıtlamalar tek bir parametre uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle sınırlandırMaz:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL'yi doğrulayan ve CLR türüne dönüştürülen `int` (örneğin `DateTime`veya) her zaman değişmez kültürü kullanan yol kısıtlamaları. Bu kısıtlamalar, URL'nin yerelleştirilebilir olmadığını varsayar. Çerçeve tarafından sağlanan rota kısıtlamaları, rota değerlerinde depolanan değerleri değiştirmez. URL'den ayrıştırılan tüm rota değerleri dizeleri olarak depolanır. Örneğin, `float` kısıtlama rota değerini bir yüzer'e dönüştürmeyi dener, ancak dönüştürülen değer yalnızca bir şamandıraya dönüştürülebileceğini doğrulamak için kullanılır.

## <a name="regular-expressions"></a>Normal ifadeler

ASP.NET Core çerçevesi `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` düzenli ifade oluşturucuekler. Bu <xref:System.Text.RegularExpressions.RegexOptions> üyelerin tanımı için bkz.

Normal ifadeler, yönlendirme ve C# dili tarafından kullanılanlara benzer sınır dışı layıcılar ve belirteçler kullanır. Normal ifade belirteçleri kaçmış olmalıdır. Yönlendirmede normal `^\d{3}-\d{2}-\d{4}$` ifadeyi kullanmak için:

* İfade, kaynak kodunda `\` çift eğik çizgi karakterleri olarak `\\` dize sağlanan tek ters çizgi karakterleri olmalıdır.
* Dize kaçış `\\` karakteri kaçmak `\` için düzenli ifade bize gerekir.
* Normal `\\` [ifade, harfi harfine dize literals](/dotnet/csharp/language-reference/keywords/string)kullanırken gerektirmez.

`{`Yönlendirme parametresi delimiter karakterleri kaçmak `}` `[`için `]`, , , , `{{` `}`, `[[` `]]`ifade karakterleri çift , , , . Aşağıdaki tablo da normal bir ifade yi ve kaçan sürümü gösterir:

| Normal ifade    | Kaçan Düzenli İfade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmede kullanılan normal ifadeler genellikle caret `^` karakteriyle başlar ve dizebaşlangıç konumuyla eşleşir. İfadeler genellikle dolar işareti `$` karakteri ile sona erer ve dize sonu maç. Ve `^` `$` karakterler, normal ifadenin tüm rota parametre değeriyle eşleşmesini sağlar. `^` Ve `$` karakterler olmadan, normal ifade genellikle istenmeyen dize içinde herhangi bir alt dize eşleşir. Aşağıdaki tablo örnekler sağlar ve neden eşleştiklerini veya eşleşmediklerini açıklar.

| İfadeler   | Dize    | Eşleştirme | Açıklama               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | Mz        | Evet   | İfadeyle eşleşir    |
| `[a-z]{2}`   | MZ        | Evet   | Servis talebine duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | `^` Bkz. `$` ve üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | `^` Bkz. `$` ve üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için [bkz.](/dotnet/standard/base-types/regular-expression-language-quick-reference)

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` `action` yalnızca rota değeri `list`ile `get`eşleşir . `create` Kısıtlamalar sözlüğüne aktarılırsa, `^(list|get|create)$` dize eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyan kısıtlamalar sözlüğünde geçirilen kısıtlamalar (şablon içinde satır içi değil) de normal ifadeler olarak kabul edilir.

## <a name="custom-route-constraints"></a>Özel rota kısıtlamaları

Yerleşik rota kısıtlamalarına ek olarak, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> arabirim uygulanarak özel rota kısıtlamaları oluşturulabilir. Arabirim, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> `Match`kısıtlama memnun ve `true` `false` aksi takdirde döndürür tek bir yöntem içerir.

Özel <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>bir kullanım için, rota kısıtlaması türü uygulamanın hizmet <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> kapsayıcısında uygulamanınkine kaydedilmelidir. A, <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> kısıtlama anahtarlarını bu <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> kısıtlamaları doğrulayan uygulamalara yönlendiren bir sözlüktür. Bir uygulama, <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> hizmetlerin bir `Startup.ConfigureServices` parçası olarak [güncellenebilir. Çağrı ekleme](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan . `services.Configure<RouteOptions>` Örneğin:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Kısıtlama daha sonra, kısıtlama türünü kaydederken belirtilen ad kullanılarak, rotalara olağan şekilde uygulanabilir. Örneğin:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Parametre transformatör referansı

Parametre transformatörleri:

* Bir <xref:Microsoft.AspNetCore.Routing.Route>bağlantı oluştururken çalıştırın .
* Uygulayın. `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`
* 'yi kullanarak <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>yapılandırılır.
* Parametrenin rota değerini alın ve yeni bir dize değerine dönüştürün.
* Oluşturulan bağlantıda dönüştürülen değeri kullanarak sonuçlanır.

Örneğin, rota `slugify` deseni `blog\{article:slugify}` özel `Url.Action(new { article = "MyTestArticle" })` bir parametre `blog\my-test-article`transformatörü üretir.

Bir rota deseninde bir parametre transformatörü <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`kullanmak için, ilk olarak şu şekilde yapılandırın:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametre transformatörleri, bir uç noktanın çözüldüğü yerde URI'yi dönüştürmek için çerçeve tarafından kullanılır. Örneğin, Core MVC ASP.NET, `area`bir , , `controller` `action`, ve `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Önceki rota ile eylem `SubscriptionManagementController.GetAll` URI `/subscription-management/get-all`ile eşleşir. Parametre transformatörü, bağlantı oluşturmak için kullanılan rota değerlerini değiştirmez. Örneğin, `Url.Action("GetAll", "SubscriptionManagement")` `/subscription-management/get-all`çıktılar.

ASP.NET Core, oluşturulan rotalara sahip bir parametre transformatörleri kullanmak için API kuralları sağlar:

* ASP.NET Core MVC `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API kuralına sahiptir. Bu kural, uygulamadaki tüm öznitelik rotalarına belirtilen bir parametre transformatörü uygular. Parametre transformatörü, bağlantı yolu belirteçlerini değiştirildikleri gibi dönüştürür. Daha fazla bilgi için bkz: [Belirteç değiştirmesini özelleştirmek için bir parametre transformatörü kullanın.](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)
* Razor `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` Pages'in API kuralı vardır. Bu kural, otomatik olarak keşfedilen tüm Razor Sayfalar için belirtilen bir parametre transformatörü uygular. Parametre transformatörü, Razor Pages yollarının klasör ve dosya adı bölümlerini dönüştürür. Daha fazla bilgi için bkz. [Sayfa rotalarını özelleştirmek için bir parametre transformatörü kullanın.](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Aşağıdaki örnek, rota değerleri sözlüğü verilen bir rotaya bağlantı <xref:Microsoft.AspNetCore.Routing.RouteCollection>nın nasıl oluşturileceğini gösterir ve bir .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Önceki <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> örneğin sonunda `/package/create/123`oluşturulan. Sözlük, "Paket Rotasını `operation` Takip Et" şablonunun ve `id` rota değerlerini sağlar. `package/{operation}/{id}` Ayrıntılar için, Yönlendirme Aracı [Kullan](#use-routing-middleware) bölümündeki örnek kodu veya [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)bakın.

Oluşturucuya ikinci parametre ortam değerleri topluluğudur. *ambient values* <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> Ortam değerleri, geliştiricinin istek bağlamında belirtmesi gereken değer sayısını sınırladıkları için kullanımı uygundur. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Core MVC `About` uygulamasının ASP.NET `HomeController`eyleminde, ortam değerinin `Index` &mdash; `Home` kullanıldığı eyleme bağlantı vermek için denetleyici rota değerini belirtmeniz gerekmez.

Parametreyle eşleşmeyan ortam değerleri yoksayılır. Ortam değerleri, açıkça sağlanan bir değer ortam değerini geçersiz kıldığında da yoksayılır. Eşleştirme URL'de soldan sağa gerçekleşir.

Açıkça sağlanan ancak rotanın bir kesimiyle eşleşmeyan değerler sorgu dizesine eklenir. Aşağıdaki tablo, rota şablonu `{controller}/{action}/{id?}`kullanırken sonucu gösterir.

| Ortam Değerleri                     | Açık Değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "Ana Sayfa"                | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | controller = "Order", action = "About" | `/Order/About`          |
| denetleyici = "Ev", renk = "Kırmızı" | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Bir rotanın bir parametreye karşılık gelen varsayılan değeri varsa ve bu değer açıkça sağlanmışsa, varsayılan değerle eşleşmesi gerekir:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Bağlantı oluşturma yalnızca eşleşen değerler sağlandığında ve `controller` `action` sağlandığında bu yol için bir bağlantı oluşturur.

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin), `[Route("/x{token}y")]`açgözlü olmayan bir şekilde sağdan sola doğru edebi eşleşerek işlenir. Karmaşık kesimlerin nasıl eşleştinle ilgili ayrıntılı bir açıklama için [bu koda](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) bakın. [Kod örneği](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) ASP.NET Core tarafından kullanılmaz, ancak karmaşık kesimlerin iyi bir açıklamasını sağlar.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Yönlendirme, işleyicileri yönlendirmek ve gelen istekleri göndermek için istek URI'lerinin eşlemelerinden sorumludur. Rotalar uygulamada tanımlanır ve uygulama başladığında yapılandırılır. Bir rota isteğe bağlı olarak istekte bulunan URL'den değerleri ayıklayabilir ve bu değerler daha sonra istek işleme için kullanılabilir. Uygulamadan yapılandırılmış rotaları kullanarak yönlendirme, işleyicileri yönlendirmek için eşlenen URL'ler oluşturabilir.

Core 2.1ASP.NETdeki en son yönlendirme senaryolarını kullanmak için Aşağıdaki lerde MVC `Startup.ConfigureServices`hizmetleri kaydına uyumluluk [sürümünü](xref:mvc/compatibility-version) belirtin:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Bu belge, düşük düzeyli ASP.NET Çekirdek yönlendirmeyi kapsar. ASP.NET Core MVC yönlendirmesi hakkında <xref:mvc/controllers/routing>bilgi için bkz. Razor Pages'deki yönlendirme kuralları hakkında daha <xref:razor-pages/razor-pages-conventions>fazla bilgi için bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Yönlendirme temelleri

Çoğu uygulama, URL'lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel `{controller=Home}/{action=Index}/{id?}`rota:

* Temel ve açıklayıcı yönlendirme düzenini destekler.
* UI tabanlı uygulamalar için yararlı bir başlangıç noktasıdır.

Geliştiriciler genellikle özel durumlarda bir uygulamanın yüksek trafik alanlarına ek rotalar ekler (örneğin, blog ve e-ticaret bitiş noktaları) [öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing) veya özel geleneksel rotalar kullanarak.

Web API'leri, uygulamanın işlevselliğini işlemlerin HTTP fiilleri tarafından temsil edildiği bir kaynak kümesi olarak modellemek için öznitelik yönlendirmesini kullanmalıdır. Bu, aynı mantıksal kaynaktaki birçok işlemin (örneğin GET, POST) aynı URL'yi kullanacağı anlamına gelir. Öznitelik yönlendirme, bir API'nin genel uç nokta düzenini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Razor Pages uygulamaları, bir uygulamanın *Sayfalar* klasöründe adlandırılmış kaynaklara hizmet etmek için varsayılan geleneksel yönlendirmeyi kullanır. Razor Pages yönlendirme davranışını özelleştirmenize olanak tanıyan ek kurallar mevcuttur. Daha fazla bilgi için <xref:razor-pages/index> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

URL oluşturma desteği, uygulamanın sıkı kodlama URL'leri olmadan geliştirilen uygulamanın birbirine bağlanmasını sağlar. Bu destek, temel bir yönlendirme yapılandırmasıyla başlayıp uygulamanın kaynak düzeni belirlendikten sonra rotaların değiştirilmesine olanak tanır.

Yönlendirme, şu yollarla <xref:Microsoft.AspNetCore.Routing.IRouter> ilgili yollar kullanır:

* *Rota işleyicilerine*gelen istekleri eşle.
* Yanıtlarda kullanılan URL'leri oluşturun.

Varsayılan olarak, bir uygulamanın tek bir rota koleksiyonu vardır. Bir istek geldiğinde, koleksiyondaki yollar koleksiyonda var oldukları sırayla işlenir. Çerçeve, koleksiyondaki her rotadaki <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> yöntemi çağırarak gelen istek URL'sini koleksiyondaki bir rotayla eşleştirmeyi dener. Yanıt, rota bilgilerine dayalı URL'ler (örneğin, yönlendirme veya bağlantılar için) oluşturmak için yönlendirmeyi kullanabilir ve böylece sabit kodlanmış URL'lerden kaçınarak korunabilirliğe yardımcı olabilir.

Yönlendirme sistemi aşağıdaki özelliklere sahiptir:

* Rota şablonu sözdizimi, tokenize rota parametreleri ile yolları tanımlamak için kullanılır.
* Geleneksel stil ve öznitelik tarzı uç nokta yapılandırması izin verilir.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>URL parametrenin belirli bir bitiş noktası kısıtlaması için geçerli bir değer bulunup içermediğini belirlemek için kullanılır.
* MVC/Razor Pages gibi uygulama modelleri, yönlendirme senaryolarının öngörülebilir bir uygulamasına sahip tüm rotalarını kaydeder.
* Yanıt, rota bilgilerine dayalı URL'ler (örneğin, yönlendirme veya bağlantılar için) oluşturmak için yönlendirmeyi kullanabilir ve böylece sabit kodlanmış URL'lerden kaçınarak korunabilirliğe yardımcı olabilir.
* URL oluşturma, rasgele genişletilebilirliği destekleyen yolları temel alınmaktadır. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>URL'ler oluşturmak için yöntemler sunar.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Yönlendirme, sınıf tarafından [ara yazılım](xref:fundamentals/middleware/index) ardışık hattına <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> bağlanır. [ASP.NET Core MVC,](xref:mvc/overview) yapılandırmasının bir parçası olarak ara yazılım boru hattına yönlendirme ekler ve MVC ve Razor Pages uygulamalarında yönlendirmeyi işler. Yönlendirmeyi bağımsız bir bileşen olarak nasıl kullanacağınızı öğrenmek için Yönlendirme [Ara ware'i Kullan](#use-routing-middleware) bölümüne bakın.

### <a name="url-matching"></a>URL eşleştirme

URL eşleştirme, yönlendirmenin bir *işleyiciye*gelen isteği gönderme işlemidir. Bu işlem URL yolundaki verilere dayanır, ancak istekteki verileri dikkate almak için genişletilebilir. İstekleri ayrı işleyicilere gönderme özelliği, bir uygulamanın boyutunu ve karmaşıklığını ölçeklemenin anahtarıdır.

Gelen istekler, <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>her rotadaki <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> yöntemi sırayla çağıran , girin. Örnek, <xref:Microsoft.AspNetCore.Routing.IRouter> [RouteContext.Handler'ı](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) null <xref:Microsoft.AspNetCore.Http.RequestDelegate>olmayan bir ' a ayarlayarak isteğin *işlenip işlenmeyeceğini* seçer. Bir rota istek için bir işleyici ayarlarsa, rota işleme durur ve işleyici isteği işlemek için çağrılır. İsteği işlemek için rota işleyicisi bulunmazsa, ara yazılım isteği istek ardışık taşındaki bir sonraki ara yazılıma iletir.

Birincil giriş, <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> geçerli istekle ilişkili [RouteContext.HttpContext'tır.](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) ve [RouteContext.RouteData,](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) bir rota eşleştikten sonra ayarlanan çıktılardır.

Çağıran <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> bir eşleşme, [RouteContext.RouteData'nın](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özelliklerini şimdiye kadar gerçekleştirilen istek işlemedayalı uygun değerlere ayarlar.

[RouteData.Values,](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) rotadan üretilen *rota değerlerinin* sözlüğüdür. Bu değerler genellikle URL'yi belirterek belirlenir ve kullanıcı girişini kabul etmek veya uygulama içinde daha fazla gönderme kararı almak için kullanılabilir.

[RouteData.DataTokens,](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) eşleşen rotayla ilgili ek verilerin yer alan bir özellik torbasıdır. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*>uygulamanın hangi rotayla eşleştiğine göre karar verebilmeleri için durum verilerinin her rotayla ilişkiye alinmasını desteklemek için sayılmısa. Bu değerler geliştirici tarafından tanımlanır ve yönlendirme davranışını hiçbir şekilde **etkilemez.** Ayrıca, [RouteData.DataTokens'de](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) saklanan değerler, string'lere dönüştürülebilen [RouteData.Values'in](xref:Microsoft.AspNetCore.Routing.RouteData.Values)aksine her türden olabilir.

[RouteData.Routers,](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) isteğin başarıyla eşleşmesinde yer alan yolların listesidir. Rotalar birbirinin içine iç içe olabilir. Özellik, <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşmeyle sonuçlanan mantıksal rota lar ağacından geçen yolu yansıtır. Genellikle, ilk öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> rota koleksiyonudur ve URL oluşturma için kullanılmalıdır. Son öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşen rota işleyicisi.

<a name="lg"></a>

### <a name="url-generation"></a>URL oluşturma

URL oluşturma, yönlendirmenin bir rota değerleri kümesine dayalı bir URL yolu oluşturabileceği işlemdir. Bu, rota işleyicileri ile bunlara erişen URL'ler arasında mantıksal bir ayrım yapılmasına olanak tanır.

URL oluşturma benzer bir yinelemeli işlem izler, ancak kullanıcı veya <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> çerçeve kodu ile rota toplama yöntemine çağıran başlar. Null olmayan <xref:Microsoft.AspNetCore.Routing.VirtualPathData> <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> döndürülene kadar her *rotanın* sırayla çağrılan bir yöntemi vardır.

Başlıca girdiler <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> şunlardır:

* [VirtualPathContext.httpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Rotalar öncelikle tarafından <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> sağlanan <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> rota değerlerini kullanır ve bir URL oluşturmanın mümkün olup olmadığına ve hangi değerlerin dahil edeceğine karar verir. Geçerli <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> istek eşleştirilebilir şekilde üretilen rota değerleri kümesidir. Buna karşılık, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> geçerli işlem için istenen URL'nin nasıl üretilmeye cerenolduğunu belirten rota değerleridir. Bir <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> rotanın geçerli bağlamla ilişkili hizmetler veya ek veriler alması durumunda sağlanır.

> [!TIP]
> [VirtualPathContext.Values'i](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)için geçersiz kılmalar kümesi olarak düşünün. URL oluşturma, aynı rota veya rota değerlerini kullanan bağlantılar için URL'ler oluşturmak için geçerli istekteki rota değerlerini yeniden kullanmaya çalışır.

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> Çıkış bir <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>bir <xref:Microsoft.AspNetCore.Routing.RouteData>paraleldir. <xref:Microsoft.AspNetCore.Routing.VirtualPathData>çıkış <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> URL'si ve rota tarafından ayarlanılması gereken bazı ek özellikleri içerir.

[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) özelliği, rota tarafından üretilen *sanal yolu* içerir. İhtiyaçlarınıza bağlı olarak, yolu daha fazla işlemeniz gerekebilir. Oluşturulan URL'yi HTML'de işlemek istiyorsanız, uygulamanın temel yolunu hazırlayın.

[VirtualPathData.Router,](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) URL'yi başarıyla oluşturan rotaya bir başvurudur.

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) özellikleri, URL'yi oluşturan rotayla ilgili ek verilerin sözlüğüdür. Bu [RouteData.DataTokens paraleldir.](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)

### <a name="create-routes"></a>Yolları oluşturma

<xref:Microsoft.AspNetCore.Routing.Route> Yönlendirme, sınıfı standart uygulama olarak <xref:Microsoft.AspNetCore.Routing.IRouter>sağlar. <xref:Microsoft.AspNetCore.Routing.Route>çağrıldığında <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> URL yolu ile eşleşecek desenleri tanımlamak için *rota şablonu* sözdizimini kullanır. <xref:Microsoft.AspNetCore.Routing.Route>çağrıldığında <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> bir URL oluşturmak için aynı rota şablonu kullanır.

Çoğu uygulama arayarak <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> veya üzerinde <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>tanımlanan benzer uzantı yöntemlerinden birini arayarak rotalar oluşturur. Uzantı <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> yöntemlerinden herhangi biri <xref:Microsoft.AspNetCore.Routing.Route> bir örnek oluşturur ve rota koleksiyonuna ekler.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>rota işleyicisi parametrekabul etmez. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>yalnızca <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Varsayılan işleyici bir `IRouter`' dir ve işleyici isteği işlemeyebilir. Örneğin, ASP.NET Core MVC genellikle yalnızca kullanılabilir bir denetleyici ve eylem eşleşen istekleri işleyen varsayılan işleyici olarak yapılandırılır. MVC'de yönlendirme hakkında daha fazla <xref:mvc/controllers/routing>bilgi edinmek için bkz.

Aşağıdaki kod örneği, tipik <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> bir ASP.NET Core MVC rota tanımı tarafından kullanılan bir çağrı örneğidir:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Bu şablon bir URL yolu yla eşleşir ve rota değerlerini ayıklar. Örneğin, yol `/Products/Details/17` aşağıdaki rota değerlerini oluşturur: `{ controller = Products, action = Details, id = 17 }`.

Rota değerleri, URL yolunu n için bölümlere bölerek ve her kesimin rota şablonundaki *rota parametre* adı ile eşleştirilerek belirlenir. Rota parametreleri adlandırılır. Parametre adı ayraçlara `{ ... }`ekleyerek tanımlanan parametreler.

Önceki şablon da URL yolu `/` eşleşebilir `{ controller = Home, action = Index }`ve değerleri üretmek. Bu, ve `{controller}` `{action}` rota parametrelerinin varsayılan `id` değerlere sahip olması ve rota parametresi isteğe bağlı olması nedeniyle oluşur. Eşitler işareti`=`( ) ardından rota parametresi adı parametre için varsayılan bir değer tanımlar. Rota parametresi adı sonra bir soru işareti (`?`) isteğe bağlı bir parametre tanımlar.

Varsayılan değeri olan rota parametreleri, rota eşleştiğinde *her zaman* bir rota değeri üretir. İlgili URL yolu kesimi yoksa, isteğe bağlı parametreler rota değeri oluşturmaz. Rota [şablonu](#route-template-reference) senaryoları ve sözdiziminin ayrıntılı bir açıklaması için Rota şablonu başvuru bölümüne bakın.

Aşağıdaki örnekte, rota parametre `{id:int}` tanımı rota parametresi için bir [rota kısıtlaması](#route-constraint-reference) `id` tanımlar:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Bu şablon, gibi `/Products/Details/17` bir `/Products/Details/Apples`URL yolu eşleşir ama değil. Rota kısıtlamaları, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> bunları doğrulamak için rota değerlerini uygular ve inceler. Bu örnekte, rota `id` değerinin bir tamsayıya dönüştürülebilir olması gerekir. Çerçeve tarafından sağlanan rota kısıtlamalarının açıklaması için [rota kısıtlaması-başvuruya](#route-constraint-reference) bakın.

' `dataTokens`ve `defaults` <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> . için `constraints`ek kabul değerleri Bu parametrelerin tipik kullanımı, anonim tür eşleme rota parametre adlarının özellik adlarının bulunduğu anonim olarak yazılan bir nesneyi geçmektir.

Aşağıdaki <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> örnekler eşdeğer yollar oluşturur:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Kısıtlamaları ve varsayılanları tanımlamak için satır satır ekindizi basit yollar için uygun olabilir. Ancak, satır arasözcü tarafından desteklenmeyen veri belirteçleri gibi senaryolar da vardır.

Aşağıdaki örnekte birkaç ek senaryo gösterilmiş:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Önceki şablon gibi `/Blog/All-About-Routing/Introduction` bir URL yolu eşleşir `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`ve değerleri ayıklar. Şablonda karşılık `controller` gelen `action` rota parametreleri olmamasına rağmen, varsayılan rota değerleri için ve rota tarafından üretilir. Varsayılan değerler rota şablonunda belirtilebilir. `article` Rota parametresi, rota parametresi adından önce yıldız`*`işareti ( ) görünümüyle *tümünü yakalama* olarak tanımlanır. Tümyolu yakalama parametreleri URL yolunun geri kalanını yakalar ve boş dizeyle eşleşebilir.

Aşağıdaki örnek, rota kısıtlamaları ve veri belirteçleri ekler:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Önceki şablon gibi `/en-US/Products/5` bir URL yolu eşleşir `{ controller = Products, action = Details, id = 5 }` ve değerleri ve `{ locale = en-US }`veri belirteçleri ayıklar.

![Yerel Windows belirteçleri](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Rota sınıfı URL oluşturma

Sınıf, <xref:Microsoft.AspNetCore.Routing.Route> rota şablonuyla bir dizi rota değerini birleştirerek URL oluşturma yı da gerçekleştirebilir. Bu mantıksal olarak URL yolunu eşleştirme nin ters işlemidir.

> [!TIP]
> URL oluşturmayı daha iyi anlamak için, hangi URL'yi oluşturmak istediğinizi hayal edin ve ardından bir rota şablonunun bu URL ile nasıl eşleşeceğini düşünün. Hangi değerler üretilecek? Bu, URL oluşturmanın <xref:Microsoft.AspNetCore.Routing.Route> sınıfta nasıl çalıştığının kaba karşılığıdır.

Aşağıdaki örnekte genel bir ASP.NET Core MVC varsayılan rota kullanır:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Rota değerleri `{ controller = Products, action = List }`yle URL `/Products/List` oluşturulur. Rota değerleri, URL yolunu oluşturmak için ilgili rota parametreleri için ikame edilir. İsteğe bağlı bir rota parametresi olduğundan, `id` URL `id`için bir değer olmadan başarıyla oluşturulur.

Rota değerleri `{ controller = Home, action = Index }`yle URL `/` oluşturulur. Sağlanan rota değerleri varsayılan değerlerle eşleşir ve varsayılan değerlere karşılık gelen segmentler güvenli bir şekilde atlanır.

Her iki URL de aşağıdaki rota tanımıyla`/Home/Index` `/`(ve) URL oluşturmak için kullanılan aynı rota değerlerini kullanarak gidiş-dönüş oluşturdu.

> [!NOTE]
> Core MVC'ASP.NET kullanan <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> bir uygulama, doğrudan yönlendirmeye çağırmak yerine URL'ler oluşturmak için kullanmalıdır.

URL oluşturma hakkında daha fazla bilgi için [Url oluşturma başvuru](#url-generation-reference) bölümüne bakın.

## <a name="use-routing-middleware"></a>Yönlendirme ara yazılımlarını kullanma

Uygulamanın proje dosyasındaki [Microsoft.AspNetCore.App meta paketine](xref:fundamentals/metapackage-app) başvurun.

Aşağıdaki durumlarda servis konteynerine `Startup.ConfigureServices`yönlendirme ekleme:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Rotalar `Startup.Configure` yöntemde yapılandırılmalıdır. Örnek uygulama aşağıdaki API'leri kullanır:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Yalnızca HTTP GET istekleriyle eşleşir.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Aşağıdaki tablo, verilen ÜRB'lerle yanıtları gösterir.

| URI                    | Yanıt                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Merhaba! Rota değerleri: [işlem, oluşturma], [id, 3] |
| `/package/track/-3`    | Merhaba! Rota değerleri: [işlem, parça], [id, -3] |
| `/package/track/-3/`   | Merhaba! Rota değerleri: [işlem, parça], [id, -3] |
| `/package/track/`      | İstek suya düşüyor, eşleşme yok.              |
| `GET /hello/Joe`       | Merhaba Joe!                                          |
| `POST /hello/Joe`      | İstek ile düşer, maçlar HTTP GET sadece. |
| `GET /hello/Joe/Smith` | İstek suya düşüyor, eşleşme yok.              |

Tek bir rotayı yapılandırıyorsanız, <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> bir `IRouter` örnekte geçeni arayın. Kullanmanız <xref:Microsoft.AspNetCore.Routing.RouteBuilder>gerekmez.

Çerçeve, rotalar oluşturmak için bir<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>dizi uzatma yöntemi sağlar ( ):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Listelenen yöntemlerden bazıları, <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>örneğin <xref:Microsoft.AspNetCore.Http.RequestDelegate>, bir . Rota <xref:Microsoft.AspNetCore.Http.RequestDelegate> eşleştiğinde *rota işleyicisi* olarak kullanılır. Bu ailedeki diğer yöntemler, rota işleyicisi olarak kullanılmak üzere bir ara yazılım ardışık yapılandırmaizine olanak sağlar. `Map*` Yöntem, <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>".

Yöntemler, `Map[Verb]` yöntem adında http fiiliile yolu sınırlamak için kısıtlamalar kullanır. Örneğin, bkz <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>ve .

## <a name="route-template-reference"></a>Rota şablonu başvurusu

Kıvırcık ayraçlar içindeki`{ ... }`belirteçler ( ) rota eşleştiğinde bağlanan *rota parametrelerini* tanımlar. Bir rota segmentinde birden fazla rota parametresi tanımlayabilirsiniz, ancak bunların gerçek bir değerle ayrılması gerekir. Örneğin, `{controller=Home}{action=Index}` geçerli bir yol değildir, çünkü ile `{controller}` . `{action}` Bu rota parametrelerinin bir adı olmalıdır ve ek öznitelikler belirtilmiş olabilir.

Rota parametreleri dışındaki gerçek metin `{id}`(örneğin, ) `/` ve yol ayırıcısı URL'deki metyle eşleşmelidir. Metin eşleştirme büyük/küçük harf duyarsız ve URL'ler yolunun deşifre gösterimine dayanır. Bir literal rota parametresi`{` delimiter `}``{{` (veya) eşleştirmek için , `}}`karakter (veya ) tekrarlayarak delimiter kaçış.

İsteğe bağlı dosya uzantısı ile bir dosya adını yakalamaya çalışan URL desenleri ek hususlar vardır. Örneğin, şablonu `files/{filename}.{ext?}`göz önünde bulundurun. Her ikisi `filename` için `ext` değerler ve var olduğunda, her iki değer de doldurulur. URL'de yalnızca `filename` bir değer varsa, rota eşleşir,`.`çünkü son daki dönem ( ) isteğe bağlıdır. Aşağıdaki URL'ler bu rotayla eşleşir:

* `/files/myFile.txt`
* `/files/myFile`

Uri'nin geri kalanına`*`bağlamak için rota parametresine önek olarak yıldız işaretini kullanabilirsiniz. Buna *catch-all* parametresi denir. Örneğin, `blog/{*slug}` `slug` rota değerine atanan `/blog` uri ile başlayan ve onu izleyen herhangi bir değere sahip herhangi bir URI ile eşleşir. Tümlerini yakalama parametreleri boş dizeyle de eşleşebilir.

Tümlerini yakalama parametresi, yol ayırıcısı (`/`) karakterleri de dahil olmak üzere bir URL oluşturmak için kullanıldığında uygun karakterlerden kaçar. Örneğin, rota `foo/{*path}` değerleri `{ path = "my/path" }` ile rota `foo/my%2Fpath`oluşturur. Kaçan ileri eğik çizgiye dikkat edin.

Rota parametreleri, eşitler işaretiyle ayrılan parametre adı ()`=`ile ayrıldıktan sonra varsayılan değeri belirterek varsayılan *değerlere* sahip olabilir. Örneğin, `{controller=Home}` varsayılan `Home` değer olarak `controller`tanımlar. Parametrenin URL'sinde değer yoksa varsayılan değer kullanılır. Rota parametreleri, parametre adının sonuna`?`bir soru işareti ( ) ekleyerek `id?`isteğe bağlı hale getirilir. İsteğe bağlı değerler ve varsayılan rota parametreleri arasındaki fark, varsayılan&mdash;değeri olan bir rota parametresinin her zaman isteğe bağlı bir parametrenin yalnızca istek URL'si tarafından bir değer sağlandığında bir değere sahip olduğu bir değer üretmesidir.

Rota parametrelerinde URL'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamalar olabilir. Rota parametre`:`adı sonra bir iki nokta üst üste ( ) ve kısıtlama adı ekleme bir rota parametresi üzerinde *satır içinde bir kısıtlama* belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, kısıtlama adından`(...)`sonra parantez içinde ( ) ekolarak konum. Birden çok satır içi kısıtlamalar başka bir`:`üst üste ( ) ve kısıtlama adı ekleyerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, URL işlemede kullanılacak bir örnek oluşturmak için <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> hizmete aktarılır. Örneğin, rota şablonu `blog/{article:minlength(10)}` bağımsız `minlength` değişkenile `10`bir kısıtlama belirtir. Rota kısıtlamaları ve çerçeve tarafından sağlanan kısıtlamaların listesi hakkında daha fazla bilgi için [Rota kısıtlaması başvuru](#route-constraint-reference) bölümüne bakın.

Aşağıdaki tabloörnek rota şablonlarını ve davranışlarını gösterir.

| Rota Şablonu                           | Örnek Eşleşen URI    | İstek URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Sadece tek yol `/hello`eşleşir.                                     |
| `{Page=Home}`                            | `/`                     | Maçlar ve `Page` `Home`ayarlar .                                         |
| `{Page=Home}`                            | `/Contact`              | Maçlar ve `Page` `Contact`ayarlar .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` Denetleyici ve `List` eylem haritaları.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` Denetleyici ve `Details` eylem haritaları (123`id` olarak ayarlayın). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` Denetleyici ve `Index` yönteme haritalar (yoksayılır).`id`        |

Şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar da rota şablonu dışında belirtilebilir.

> [!TIP]
> Yerleşik yönlendirme uygulamalarının <xref:Microsoft.AspNetCore.Routing.Route>istekleri nasıl eşleştirbildiğini görmek için [Günlüğe Kaydetme'yi](xref:fundamentals/logging/index) etkinleştirin.

## <a name="route-constraint-reference"></a>Rota kısıtlaması başvurusu

Gelen URL'de bir eşleşme oluştuğunda ve URL yolu rota değerlerine belirtildiğinde rota kısıtlamaları yürütülür. Rota kısıtlamaları genellikle rota şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda evet/hayır kararı verir. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilemeyeceğini değerlendirmek için rota değerinin dışındaki verileri kullanır. Örneğin, http <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> fiiline dayalı bir isteği kabul edebilir veya reddedebilir. Kısıtlamalar yönlendirme istekleri ve bağlantı oluşturma kullanılır.

> [!WARNING]
> **Giriş doğrulama**için kısıtlamalar kullanmayın. **Giriş doğrulaması**için kısıtlamalar kullanılıyorsa, geçersiz giriş sonuçları *404 - 400* yerine Bulunamadı yanıt - Uygun bir hata iletisi ile *Hatalı İstek.* Rota kısıtlamaları, belirli bir rotanın girdilerini doğrulamak için değil, benzer yolları **ayrıştırmak** için kullanılır.

Aşağıdaki tablo, örnek rota kısıtlamaları ve bunların beklenen davranışlarını gösterir.

| kısıtlama | Örnek | Örnek Eşleşmeler | Notlar |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir karşıcıyla eşleşir |
| `bool` | `{active:bool}` | `true`, `FALSE` | `true` Kibrit `false` ler veya (büyük/küçük harf duyarsız) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Değişmez kültürdeki geçerli `DateTime` bir değerle eşleşir. Önceki uyarıya bakın.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Değişmez kültürdeki geçerli `decimal` bir değerle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `double` bir değerle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Değişmez kültürdeki geçerli `float` bir değerle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Geçerli `Guid` bir değerle eşleşir |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli `long` bir değerle eşleşir |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String en az 4 karakter olmalıdır |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Dize en fazla 8 karakter olmalıdır |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String tam olarak 12 karakter uzunluğunda olmalıdır |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String en az 8 ve en fazla 16 karakter uzunluğunda olmalıdır |
| `min(value)` | `{age:min(18)}` | `19` | İntesayı değeri en az 18 olmalıdır |
| `max(value)` | `{age:max(120)}` | `91` | Tümsek değeri 120'den fazla olmamalıdır |
| `range(min,max)` | `{age:range(18,120)}` | `91` | İntesayı değeri en az 18 olmalı, ancak en fazla 120 |
| `alpha` | `{name:alpha}` | `Rick` | String bir veya daha fazla alfabetik karakterden`a`-`z`(, büyük/küçük harf duyarsız) oluşmalıdır |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String normal ifadeyle eşleşmelidir (normal ifade tanımlama yla ilgili ipuçlarına bakın) |
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre dışı bir değerin mevcut olduğunu zorlamak için kullanılır |

Birden çok, kolon-delimited kısıtlamalar tek bir parametre uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle sınırlandırMaz:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL'yi doğrulayan ve CLR türüne dönüştürülen `int` (örneğin `DateTime`veya) her zaman değişmez kültürü kullanan yol kısıtlamaları. Bu kısıtlamalar, URL'nin yerelleştirilebilir olmadığını varsayar. Çerçeve tarafından sağlanan rota kısıtlamaları, rota değerlerinde depolanan değerleri değiştirmez. URL'den ayrıştırılan tüm rota değerleri dizeleri olarak depolanır. Örneğin, `float` kısıtlama rota değerini bir yüzer'e dönüştürmeyi dener, ancak dönüştürülen değer yalnızca bir şamandıraya dönüştürülebileceğini doğrulamak için kullanılır.

## <a name="regular-expressions"></a>Normal ifadeler

ASP.NET Core çerçevesi `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` düzenli ifade oluşturucuekler. Bu <xref:System.Text.RegularExpressions.RegexOptions> üyelerin tanımı için bkz.

Normal ifadeler, Yönlendirme ve C# dili tarafından kullanılanlara benzer sınır dışı layıcılar ve belirteçler kullanır. Normal ifade belirteçleri kaçmış olmalıdır. Yönlendirmede normal `^\d{3}-\d{2}-\d{4}$` ifadeyi kullanmak için, ifadenin `\` `\` dize kaçış karakterinden kaçmak için `\\` [(harfi harfine dize literalleri](/dotnet/csharp/language-reference/keywords/string)kullanmadığı sürece) C# kaynak dosyasında (çift eğik çizgi) karakter olarak dizede (çift eğik çizgi) karakter olarak sağlanmış olması gerekir. Yönlendirme parametresi delimiter karakterleri kaçmak`{` `}`için `[` `]`( , , , ,`{{` `}`, `[[` `]]`, , , ifade deki karakterleri iki katına ( , , , , . Aşağıdaki tabloda normal bir ifade ve kaçan sürüm gösterilmektedir.

| Normal ifade    | Kaçan Düzenli İfade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmede kullanılan normal ifadeler genellikle caret (`^`) karakteriyle başlar ve dizebaşlangıç pozisyonuyla eşleşir. İfadeler genellikle dolar işareti (`$`) karakteri ve dize sonu maç ile sona erer. Ve `^` `$` karakterler, normal ifadenin tüm rota parametre değeriyle eşleşmesini sağlar. `^` Ve `$` karakterler olmadan, normal ifade genellikle istenmeyen dize içinde herhangi bir alt dize eşleşir. Aşağıdaki tablo örnekler sağlar ve neden eşleştiklerini veya eşleşmediklerini açıklar.

| İfadeler   | Dize    | Eşleştirme | Açıklama               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Evet   | Substring eşleşmeleri     |
| `[a-z]{2}`   | Mz        | Evet   | İfadeyle eşleşir    |
| `[a-z]{2}`   | MZ        | Evet   | Servis talebine duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | `^` Bkz. `$` ve üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | `^` Bkz. `$` ve üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için [bkz.](/dotnet/standard/base-types/regular-expression-language-quick-reference)

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` `action` yalnızca rota değeri `list`ile `get`eşleşir . `create` Kısıtlamalar sözlüğüne aktarılırsa, `^(list|get|create)$` dize eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyan kısıtlamalar sözlüğünde geçirilen kısıtlamalar (şablon içinde satır içi değil) de normal ifadeler olarak kabul edilir.

## <a name="custom-route-constraints"></a>Özel Rota Kısıtlamaları

Yerleşik rota kısıtlamalarına ek olarak, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> arabirim uygulanarak özel rota kısıtlamaları oluşturulabilir. Arabirim, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> `Match`kısıtlama memnun ve `true` `false` aksi takdirde döndürür tek bir yöntem içerir.

Özel <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>bir kullanım için, rota kısıtlaması türü uygulamanın hizmet <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> kapsayıcısında uygulamanınkine kaydedilmelidir. A, <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> kısıtlama anahtarlarını bu <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> kısıtlamaları doğrulayan uygulamalara yönlendiren bir sözlüktür. Bir uygulama, <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> hizmetlerin bir `Startup.ConfigureServices` parçası olarak [güncellenebilir. Çağrı ekleme](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan . `services.Configure<RouteOptions>` Örneğin:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Kısıtlama daha sonra, kısıtlama türünü kaydederken belirtilen ad kullanılarak, rotalara olağan şekilde uygulanabilir. Örneğin:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Aşağıdaki örnek, rota değerleri sözlüğü verilen bir rotaya bağlantı <xref:Microsoft.AspNetCore.Routing.RouteCollection>nın nasıl oluşturileceğini gösterir ve bir .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Önceki <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> örneğin sonunda `/package/create/123`oluşturulan. Sözlük, "Paket Rotasını `operation` Takip Et" şablonunun ve `id` rota değerlerini sağlar. `package/{operation}/{id}` Ayrıntılar için, Yönlendirme Aracı [Kullan](#use-routing-middleware) bölümündeki örnek kodu veya [örnek uygulamaya](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)bakın.

Oluşturucuya ikinci parametre ortam değerleri topluluğudur. *ambient values* <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> Ortam değerleri, geliştiricinin istek bağlamında belirtmesi gereken değer sayısını sınırladıkları için kullanımı uygundur. Geçerli isteğin geçerli rota değerleri bağlantı oluşturma için ortam değerleri olarak kabul edilir. Core MVC `About` uygulamasının ASP.NET `HomeController`eyleminde, ortam değerinin `Index` &mdash; `Home` kullanıldığı eyleme bağlantı vermek için denetleyici rota değerini belirtmeniz gerekmez.

Parametreyle eşleşmeyan ortam değerleri yoksayılır. Ortam değerleri, açıkça sağlanan bir değer ortam değerini geçersiz kıldığında da yoksayılır. Eşleştirme URL'de soldan sağa gerçekleşir.

Açıkça sağlanan ancak rotanın bir kesimiyle eşleşmeyan değerler sorgu dizesine eklenir. Aşağıdaki tablo, rota şablonu `{controller}/{action}/{id?}`kullanırken sonucu gösterir.

| Ortam Değerleri                     | Açık Değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "Ana Sayfa"                | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | controller = "Order", action = "About" | `/Order/About`          |
| denetleyici = "Ev", renk = "Kırmızı" | action = "Hakkında"                       | `/Home/About`           |
| denetleyici = "Ana Sayfa"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Bir rotanın bir parametreye karşılık gelen varsayılan değeri varsa ve bu değer açıkça sağlanmışsa, varsayılan değerle eşleşmesi gerekir:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Bağlantı oluşturma yalnızca eşleşen değerler sağlandığında ve `controller` `action` sağlandığında bu yol için bir bağlantı oluşturur.

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin), `[Route("/x{token}y")]`açgözlü olmayan bir şekilde sağdan sola doğru edebi eşleşerek işlenir. Karmaşık kesimlerin nasıl eşleştinle ilgili ayrıntılı bir açıklama için [bu koda](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) bakın. [Kod örneği](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) ASP.NET Core tarafından kullanılmaz, ancak karmaşık kesimlerin iyi bir açıklamasını sağlar.

::: moniker-end
