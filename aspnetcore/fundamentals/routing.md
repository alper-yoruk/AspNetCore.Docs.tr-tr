---
title: ASP.NET Core yönlendirme
author: rick-anderson
description: HTTP isteklerinden eşleşen ve yürütülebilir uç noktalara gönderen ASP.NET Core yönlendirmenin nasıl sorumlu olduğunu öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
no-loc:
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
uid: fundamentals/routing
ms.openlocfilehash: e3dd7168e6974f63fa963d3732bc5df41814c70e
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491632"
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core yönlendirme

[Ryan şimdi ak](https://github.com/rynowak), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Yönlendirme, gelen HTTP isteklerini eşleştirmekten ve bu istekleri uygulamanın yürütülebilir uç noktalarına gönderen sorumludur. [Uç noktalar](#endpoint) , uygulamanın yürütülebilir istek işleme kodu birimleridir. Uç noktalar uygulamada tanımlanır ve uygulama başlatıldığında yapılandırılır. Uç nokta eşleştirme işlemi, isteğin URL 'sindeki değerleri çıkarabilir ve bu değerleri istek işleme için sağlayabilir. Uygulamanın uç nokta bilgilerini kullanarak, yönlendirme, uç noktalarıyla eşlenen URL 'Ler de oluşturabilir.

Uygulamalar, kullanarak yönlendirmeyi yapılandırabilir:

- Denetleyiciler
- Razor Sayfaları
- SignalR
- gRPC Hizmetleri
- [Sistem durumu denetimleri](xref:host-and-deploy/health-checks)gibi uç nokta özellikli [Ara yazılım](xref:fundamentals/middleware/index) .
- Yönlendirme ile kayıtlı temsilciler ve Lambdalar.

Bu belgede ASP.NET Core yönlendirmenin alt düzey ayrıntıları ele alınmaktadır. Yönlendirmeyi yapılandırma hakkında bilgi için:

* Denetleyiciler için bkz <xref:mvc/controllers/routing> ..
* RazorSayfa kuralları için bkz <xref:razor-pages/razor-pages-conventions> ..

Bu belgede açıklanan uç nokta yönlendirme sistemi, ASP.NET Core 3,0 ve üzeri için geçerlidir. Önceki yönlendirme sistemiyle ilgili daha fazla bilgi için <xref:Microsoft.AspNetCore.Routing.IRouter> , aşağıdaki yaklaşımlardan birini kullanarak ASP.NET Core 2,1 sürümünü seçin:

* Önceki sürümün sürüm Seçicisi.
* [ASP.NET Core 2,1 yönlendirme](?view=aspnetcore-2.1)öğesini seçin.

[Örnek kodu görüntüleme veya indirme](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu belgenin karşıdan yükleme örnekleri belirli bir sınıf tarafından etkinleştirilir `Startup` . Belirli bir örneği çalıştırmak için, *program.cs* öğesini istenen sınıfı çağırmak üzere değiştirin `Startup` .

## <a name="routing-basics"></a>Yönlendirme temelleri

Tüm ASP.NET Core şablonları oluşturulan koda yönlendirmeyi içerir. Yönlendirme, içindeki [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzenine kaydedilir `Startup.Configure` .

Aşağıdaki kod, yönlendirmenin temel bir örneğini göstermektedir:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Yönlendirme, ve tarafından kaydedilen bir dizi ara yazılımı <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> kullanır <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> :

* `UseRouting` ara yazılım ardışık düzenine eşleşen rota ekler. Bu ara yazılım, uygulamada tanımlanan uç nokta kümesine bakar ve isteğe bağlı olarak [en iyi eşleşmeyi](#urlm) seçer.
* `UseEndpoints` uç nokta yürütmeyi ara yazılım ardışık düzenine ekler. Seçili uç noktayla ilişkili temsilciyi çalıştırır.

Önceki örnekte, [Mapget](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) yöntemi kullanılarak kod uç noktasına tek bir *yol* dahildir:

* `GET`Kök URL 'ye BIR http isteği gönderildiğinde `/` :
  * Gösterilen istek temsilcisi yürütülüyor.
  * `Hello World!` HTTP yanıtına yazılır. Varsayılan olarak, kök URL 'sidir `/` `https://localhost:5001/` .
* İstek yöntemi yoksa `GET` veya kök URL 'si değilse `/` , hiçbir yol eşleşmesi ve bir HTTP 404 döndürülür.

### <a name="endpoint"></a>Uç Nokta

<a name="endpoint"></a>

`MapGet`Yöntemi, bir **uç noktayı**tanımlamak için kullanılır. Uç nokta şöyle olabilir:

* URL ve HTTP yöntemiyle eşleştirerek seçilir.
* , Temsilcisi çalıştırılarak yürütülür.

Uygulama tarafından eşleştirilecek ve çalıştırılabilen uç noktalar ' de yapılandırılır `UseEndpoints` . Örneğin,, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*> <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> ve [benzer yöntemler](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) , istek temsilcilerinizi yönlendirme sistemine bağlanır.
ASP.NET Core Framework özelliklerini yönlendirme sistemine bağlamak için ek yöntemler kullanılabilir:
- [Sayfalar Razor için harita sayfaları Razor](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [Denetleyiciler için MapControllers](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub \<THub>SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [GRPC için MapGrpcService \<TService>](xref:grpc/aspnetcore)

Aşağıdaki örnekte, daha karmaşık bir yol şablonuyla yönlendirme gösterilmektedir:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Dize `/hello/{name:alpha}` bir **yol şablonudur**. Uç noktanın nasıl eşleştirileceği yapılandırmak için kullanılır. Bu durumda, şablon eşleşir:

* Şunun gibi bir URL `/hello/Ryan`
* `/hello/`Arkasından bir alfabetik karakter dizisi ile başlayan herhangi BIR URL yolu.  `:alpha` yalnızca alfabetik karakterlerle eşleşen bir rota kısıtlaması uygular. [Yol kısıtlamaları](#route-constraint-reference) bu belgenin ilerleyen kısımlarında açıklanmıştır.

URL yolunun ikinci segmenti `{name:alpha}` :

* `name`Parametreye bağlanır.
* Yakalanır ve [HttpRequest. RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*)'da depolanır.

Bu belgede açıklanan uç nokta yönlendirme sistemi ASP.NET Core 3,0 itibariyle yenidir. Ancak, ASP.NET Core tüm sürümleri aynı yol şablonu özellikleri ve yol kısıtlamaları kümesini destekler.

Aşağıdaki örnek, [durum denetimleri](xref:host-and-deploy/health-checks) ve yetkilendirmeyle yönlendirmeyi gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Yukarıdaki örnekte nasıl yapılacağı gösterilmektedir:

* Yetkilendirme ara yazılımı yönlendirmeyle birlikte kullanılabilir.
* Uç noktalar, yetkilendirme davranışını yapılandırmak için kullanılabilir.

<xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>Çağrı bir sistem durumu denetim uç noktası ekler. <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>Bu çağrıda zincir, bir yetkilendirme ilkesini uç noktaya iliştirir.

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> Kimlik doğrulama ve yetkilendirme ara yazılımını çağırarak ve ekler. Bu ara yazılım, ile arasında yerleştirilir <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> ve `UseEndpoints` şunları yapabilir:

* Hangi uç noktanın seçili olduğunu görün `UseRouting` .
* Uç noktaya göndermeden önce bir yetkilendirme ilkesi uygulayın <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Uç nokta meta verileri

Yukarıdaki örnekte, iki uç nokta bulunur, ancak yalnızca sistem durumu denetimi uç noktasına bağlı bir yetkilendirme ilkesi vardır. İstek sistem durumu denetimi uç noktasıyla eşleşiyorsa, `/healthz` bir yetkilendirme denetimi gerçekleştirilir. Bu, uç noktalara eklenen ek verilere sahip olduğunu gösterir. Bu ek verilere uç nokta **meta verileri**denir:

* Meta veriler, yönlendirme kullanan ara yazılım tarafından işlenebilir.
* Meta veriler herhangi bir .NET türü olabilir.

## <a name="routing-concepts"></a>Yönlendirme kavramları

Yönlendirme sistemi güçlü **uç nokta** kavramı ekleyerek, ara yazılım ardışık düzeninin üst kısmında oluşturulur. Uç noktalar, yönlendirme, yetkilendirme ve herhangi bir sayıda ASP.NET Core sistemi açısından birbirinden farklı olan uygulama işlevselliğinin birimlerini temsil eder.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>ASP.NET Core uç noktası tanımı

ASP.NET Core uç noktası:

* Yürütülebilir dosya: öğesine sahip <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> .
* Genişletilebilir: [meta veri](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) koleksiyonu vardır.
* Seçilebilir: Isteğe bağlı olarak [yönlendirme bilgileri](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)vardır.
* Numaralandırılabilir: bitiş noktaları koleksiyonu, kimden kaynağından alma yoluyla listelenebilir <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> . [DI](xref:fundamentals/dependency-injection)

Aşağıdaki kod, geçerli istekle eşleşen uç noktanın nasıl alınacağını ve inceleneceği gösterilmektedir:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Seçildiğinde, uç nokta öğesinden alınabilir `HttpContext` . Özellikleri incelenebilir. Uç nokta nesneleri sabittir ve oluşturulduktan sonra değiştirilemez. Uç noktanın en yaygın türü bir <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> . `RouteEndpoint` , yönlendirme sistemi tarafından seçilme olanağı sağlayan bilgiler içerir.

Yukarıdaki kod, [uygulama. Kullanım](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) , çevrimiçi bir [Ara yazılım](xref:fundamentals/middleware/index)yapılandırır.

<a name="mt"></a>

Aşağıdaki kod, işlem hattında nereye `app.Use` çağrdığına bağlı olarak bir uç nokta olmayabilir:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Bu önceki örnek `Console.WriteLine` , bir uç noktanın seçili olup olmadığını gösteren deyimler ekliyor. Netlik açısından örnek, belirtilen uç noktaya bir görünen ad atar `/` .

Bu kodu, ekran URL 'SI ile çalıştırma `/` :

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Bu kodu başka bir URL ile çalıştırmak şunları görüntüler:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Bu çıkış şunları gösterir:

* Bitiş noktası, çağrılmadan önce her zaman null olur `UseRouting` .
* Bir eşleşme bulunursa, uç nokta ve arasında boş değildir `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* `UseEndpoints`Bir eşleşme bulunduğunda ara yazılım **terminaldir** . [Terminal ara yazılımı](#tm) bu belgede daha sonra tanımlanır.
* `UseEndpoints`Yalnızca eşleşme bulunamadığında yürütme sonrasında ara yazılım.

`UseRouting`Ara yazılım, uç noktayı geçerli bağlama eklemek Için [setendpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) yöntemini kullanır. `UseRouting`Ara yazılımı özel mantık ile değiştirmek ve uç noktaları kullanmanın avantajlarını almaya devam etmek mümkündür. Uç noktalar, ara yazılım gibi alt düzey bir temel 'tür ve yönlendirme uygulamasıyla birlikte aktarılmaz. Çoğu uygulamanın `UseRouting` özel mantık ile değiştirme gereksinimi yoktur.

`UseEndpoints`Ara yazılım, ara yazılım ile birlikte kullanılmak üzere tasarlanmıştır `UseRouting` . Bir uç noktayı yürütmek için çekirdek mantık karmaşık değildir. <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>Uç noktayı almak için kullanın ve ardından <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> özelliğini çağırın.

Aşağıdaki kod, ara yazılımların yönlendirmeyi nasıl etkileyebileceğini veya tepki vermesini gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Yukarıdaki örnekte iki önemli kavram gösterilmektedir:

* Ara yazılım, yönlendirmenin çalıştığı verileri değiştirmeden önce çalışabilir `UseRouting` .
    * Genellikle, yönlendirme öncesinde görünen ara yazılım, veya gibi, isteğin bazı özelliğini değiştirir <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> .
* Ara yazılım, ve arasında çalıştırılabilir `UseRouting` ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> uç nokta yürütülmeden önce yönlendirmenin sonuçlarını işleyebilir.
    * Ve arasında çalışan ara `UseRouting` yazılımlar `UseEndpoints` :
      * Genellikle uç noktaları anlamak için meta verileri inceler.
      * , Ve tarafından yapıldığı gibi genellikle güvenlik kararları `UseAuthorization` verir `UseCors` .
    * Ara yazılım ve meta verilerin birleşimi, ilkelerin uç nokta başına yapılandırılmasını sağlar.

Yukarıdaki kodda, uç nokta başına ilkeleri destekleyen özel bir ara yazılım örneği gösterilmektedir. Ara yazılım, gizli verilere erişim *denetim günlüğünü* konsola yazar. Ara yazılım, meta verileri olan bir uç noktayı *denetlemek* üzere yapılandırılabilir `AuditPolicyAttribute` . Bu örnek, yalnızca hassas olarak işaretlenen bitiş noktalarının denetlendiği bir *katılım* modelini gösterir. Örneğin, güvenli olarak işaretlenmemiş her şeyi denetlemek için bu mantığı ters olarak tanımlamak mümkündür. Uç nokta meta veri sistemi esnektir. Bu mantık, kullanım örneğine uygun herhangi bir şekilde tasarlanabilir.

Önceki örnek kod, uç noktaların temel kavramlarını göstermek için tasarlanmıştır. **Örnek, üretim kullanımı için tasarlanmamıştır**. *Denetim günlüğü* ara yazılımı 'nın daha kapsamlı bir sürümü şöyle olacaktır:

* Bir dosya veya veritabanında oturum açın.
* Kullanıcı, IP adresi, hassas bitiş noktasının adı ve daha fazlası gibi ayrıntıları dahil edin.

Denetim ilkesi meta verileri, `AuditPolicyAttribute` `Attribute` denetleyiciler ve gibi sınıf tabanlı çerçeveler ile daha kolay kullanılmak üzere tanımlanır SignalR . *Kod yolu*kullanırken:

* Meta veriler bir Oluşturucu API 'siyle birlikte eklenir.
* Sınıf tabanlı çerçeveler, uç noktalar oluştururken karşılık gelen Yöntem ve sınıftaki tüm öznitelikleri içerir.

Meta veri türleri için en iyi uygulamalar, bunları arabirimler ya da öznitelikler olarak tanımlamaktır. Arabirimler ve öznitelikler kod yeniden kullanılmasına izin verir. Meta veri sistemi esnektir ve herhangi bir kısıtlama vermez.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Terminal ara yazılımını ve yönlendirmeyi karşılaştırma

Aşağıdaki kod örneği, yönlendirmeyi kullanarak ara yazılım kullanarak karşıtlıkları:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

İle gösterilen ara yazılım stili, `Approach 1:` **Terminal ara yazılımı**. Bu, eşleşen bir işlem yaptığı için Terminal ara yazılımı olarak adlandırılır:

* Yukarıdaki örnekteki eşleştirme işlemi, `Path == "/"` Ara yazılım ve `Path == "/Movie"` yönlendirme içindir.
* Bir eşleşme başarılı olduğunda, ara yazılımı çağırmak yerine bazı işlevleri yürütür ve döndürür `next` .

Bu, aramayı sonlandırdığı, bazı işlevleri yürüttüğünde ve sonra döndürdüğünden, Terminal ara yazılımı olarak adlandırılır.

Terminal ara yazılımını ve yönlendirmeyi karşılaştırma:
* Her iki yaklaşım da işleme ardışık düzeninin sonlandırılmasını sağlar:
    * Ara yazılım, komut zinciri çağırmak yerine dönerek işlem hattını sonlandırır `next` .
    * Uç noktalar her zaman terminaldir.
* Terminal ara yazılımı, işlem hattındaki ara yazılımı rastgele bir yerde konumlandırmayı sağlar:
    * Uç noktalar, konumunda yürütülür <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .
* Terminal ara yazılımı, rastgele kodun, ara yazılımın ne zaman eşleştiğini belirlemesine izin verir:
    * Özel yol eşleştirme kodu ayrıntılı ve doğru yazılması zor olabilir.
    * Yönlendirme, tipik uygulamalar için doğrudan çözümler sağlar. Çoğu uygulama özel yol eşleştirme kodu gerektirmez.
* Ve gibi ara yazılım içeren uç noktalar arabirimi `UseAuthorization` `UseCors` .
    * Veya ile Terminal ara yazılımı `UseAuthorization` kullanmak `UseCors` , yetkilendirme sistemiyle el ile arabirim oluşturma gerektirir.

Bir [uç nokta](#endpoint) her ikisini de tanımlar:

* İstekleri işlemek için bir temsilci.
* Rastgele meta veri koleksiyonu. Meta veriler, her uç noktaya eklenen ilkelere ve yapılandırmaya göre çapraz kesme sorunları uygulamak için kullanılır.

Terminal ara yazılımı etkin bir araç olabilir, ancak şunları gerektirebilir:

* Önemli miktarda kodlama ve test.
* İstenen esneklik düzeyine ulaşmak için diğer sistemlerle el ile tümleştirme.

Terminal ara yazılımı yazmadan önce yönlendirme ile tümleştirmeyi düşünün.

[Eşleme](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) ile tümleştirilen mevcut Terminal ara yazılımı <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> , genellikle bir yönlendirme duyarlı uç noktaya açılabilir. [Maphealthdenetimleri](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) , yönlendirici-Ware için bir model gösterir:
* Üzerine bir genişletme yöntemi yazın <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> .
* Kullanarak iç içe geçmiş bir ara yazılım işlem hattı oluşturun <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> .
* Yeni işlem hattına ara yazılım ekleyin. Bu durumda, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> .
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> içinde bir ara yazılım ardışık düzeni <xref:Microsoft.AspNetCore.Http.RequestDelegate> .
* `Map`' İ çağırın ve yeni ara yazılım ardışık düzenini sağlayın.
* Uzantı yönteminden tarafından sunulan Oluşturucu nesnesini döndürün `Map` .

Aşağıdaki kod, [Maphealthdenetimlerinin](xref:host-and-deploy/health-checks)kullanımını gösterir:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Yukarıdaki örnek, Oluşturucu nesnesinin neden önemli olduğunu gösterir. Oluşturucu nesnesini döndürmek, uygulama geliştiricisinin uç nokta için yetkilendirme gibi ilkeleri yapılandırmasına izin verir. Bu örnekte, sistem durumu denetimleri ara yazılımı yetkilendirme sistemiyle doğrudan tümleştirme gerektirmez.

Meta veri sistemi, Terminal ara yazılımı kullanan genişletilebilirlik yazarları tarafından karşılaşılan sorunlara yanıt olarak oluşturulmuştur. Her bir ara yazılım, yetkilendirme sistemiyle kendi tümleştirmesini uygulamak için sorunlu olur.

<a name="urlm"></a>

### <a name="url-matching"></a>URL eşleştirme

* , Yönlendirmenin bir [uç noktaya](#endpoint)gelen istekle eşleştiği işlemdir.
* URL yolu ve üst bilgilerindeki verileri temel alır.
* İstekteki verileri göz önünde bulundurmanız için genişletilebilir.

Bir yönlendirme ara yazılımı yürütüldüğünde, `Endpoint` geçerli istekten ' de bir [istek özelliğine](xref:fundamentals/request-features) bir ve rota değeri ayarlar <xref:Microsoft.AspNetCore.Http.HttpContext> :

* [HttpContext. GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) çağrısı uç noktasını alır.
* `HttpRequest.RouteValues` yol değerlerinin koleksiyonunu alır.

Yönlendirme ara yazılımı [, çalışan ara yazılım uç](xref:fundamentals/middleware/index) noktasını inceleyebilir ve işlem yapabilir. Örneğin, bir yetkilendirme ara yazılımı, bir yetkilendirme ilkesi için bitiş noktasının meta veri koleksiyonunu sorgulanamıyor. İstek işleme ardışık düzeninde bulunan tüm ara yazılım yürütüldükten sonra, seçilen uç noktanın temsilcisi çağrılır.

Uç nokta yönlendirmesinde yönlendirme sistemi tüm gönderme kararlarından sorumludur. Ara yazılım, seçili uç noktaya göre ilkeler uyguladığı için şu önemlidir:

* Güvenlik ilkelerinin dağıtımını etkileyebilecek herhangi bir karar, yönlendirme sisteminin içinde yapılır.

> [!WARNING]
> Geriye dönük uyumluluk için, bir denetleyici veya Razor sayfa uç noktası temsilcisi yürütüldüğünde, [routecontext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özellikleri, bu nedenle yapılan istek işlemeye bağlı olarak uygun değerlere ayarlanır.
>
> `RouteContext`Daha sonraki bir sürümde tür kullanılmıyor olarak işaretlenir:
>
> * `RouteData.Values`Öğesine geçirin `HttpRequest.RouteValues` .
> * `RouteData.DataTokens`Uç nokta meta verilerinden [ıdatatokensmetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) almak için geçiş yapın.

URL eşleştirme, yapılandırılabilir bir aşamalar kümesinde çalışır. Her aşamada, çıktı bir eşleşme kümesidir. Eşleşmeler kümesi bir sonraki aşamadan sonra daha dar olabilir. Yönlendirme uygulama, eşleşen uç noktalar için bir işlem sırası garantisi vermez. **Tüm** olası eşleşmeler aynı anda işlenir. URL ile eşleşen aşamalar aşağıdaki sırada oluşur. ASP.NET Core:

1. URL yolunu uç noktalar kümesine ve bunların yol şablonlarına göre işler ve **Tüm** eşleşmeleri toplar.
1. Önceki listeyi alır ve yol kısıtlamalarında başarısız olan eşleşmeleri kaldırır.
1. Önceki listeyi alır ve [Matcherpolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) örnekleri kümesi başarısız olan eşleşmeleri kaldırır.
1. Önceki listeden son bir karar vermek için [Endpointselector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) kullanır.

Uç noktaların listesi şunlara göre önceliklendirilir:

* [Routeendpoint. Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* [Yol şablonu önceliği](#rtp)

Tüm eşleşen uç noktalar, sonuna kadar her aşamada işlenir <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> . `EndpointSelector`Son aşamadır. En iyi eşleşme ile eşleştirmelerle en yüksek öncelikli uç noktayı seçer. En iyi eşleşme ile aynı önceliğe sahip başka eşleşmeler varsa, belirsiz eşleşme özel durumu oluşur.

Yol önceliği, daha yüksek öncelikli olarak verilen **daha belirli** bir yol şablonuna göre hesaplanır. Örneğin, şablonları ve şunları göz önünde bulundurun `/hello` `/{message}` :

* Her ikisi de URL yoluyla eşleşir `/hello` .
* `/hello`  daha özeldir ve bu nedenle daha yüksek önceliğe sahiptir.

Genel olarak, rota önceliği, uygulamada kullanılan URL şemaları türleri için en iyi eşleşmeyi seçme konusunda iyi bir iş olur. <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order>Belirsizliği önlemek için yalnızca gerekli olduğunda kullanın.

Yönlendirme tarafından sunulan genişletilebilirlik türleri nedeniyle, yönlendirme sisteminin belirsiz yolların önünde işlem yapmak mümkün değildir. Yol şablonları gibi bir örnek düşünün `/{message:alpha}` `/{message:int}` :

* `alpha`Kısıtlama yalnızca alfabetik karakterlerle eşleşir.
* `int`Kısıtlama yalnızca sayılarla eşleşir.
* Bu şablonlar aynı rota önceliğine sahiptir, ancak her iki eşleşen tek URL yoktur.
* Yönlendirme sistemi başlangıçta bir belirsizlik hatası bildirse, bu geçerli kullanım durumunu engeller.

> [!WARNING]
>
> İçindeki işlemlerin sırası <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , yönlendirme davranışını tek bir özel durumla etkilemez. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ve, <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> çağrıldığı sıraya göre uç noktalarına otomatik olarak bir sipariş değeri atar. Bu, yönlendirme sistemi olmadan, eski yönlendirme uygulamalarıyla aynı garantilere sahip olmayan denetleyicilerin uzun süreli davranışlarına benzetir.
>
> Yönlendirmenin eski uygulamasında, yolların işlendiği sıraya bağımlılığı olan yönlendirme genişletilebilirliği uygulamak mümkündür. ASP.NET Core 3,0 ve üzeri için uç nokta yönlendirme:
> 
> * Bir yol kavramı yoktur.
> * Sıralama garantisi sağlamaz. Tüm uç noktalar aynı anda işlenir.

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Yol şablonu önceliği ve uç nokta seçim sırası

[Yol şablonu önceliği](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) , her yol şablonuna, ne kadar belirli bir değere göre bir değer atayan bir sistemdir. Yol şablonu önceliği:

* Ortak durumlarda uç noktaların sırasını ayarlama ihtiyacını önler.
* Yönlendirme davranışının ortak Sense beklentilerini eşleştirmeye çalışır.

Örneğin, şablonları ve öğesini `/Products/List` düşünün `/Products/{id}` . `/Products/List`URL yolundan daha iyi bir eşleşme olduğunu varsaymak mantıklı olacaktır `/Products/{id}` `/Products/List` . , Değişmez değer segmentinin `/List` parametre segmentinden daha iyi önceliğe sahip olduğu kabul edildiği için geçerlidir `/{id}` .

Önceliğin nasıl kullanılacağına ilişkin ayrıntılar, yönlendirme şablonlarının nasıl tanımlandığınıza bağlıdır:

* Daha fazla kesimli şablonlar daha belirgin olarak değerlendirilir.
* Değişmez değerli bir kesim, bir parametre segmentinden daha belirgin olarak değerlendirilir.
* Kısıtlaması olan bir parametre segmenti, olmadan birden fazla olarak değerlendirilir.
* Karmaşık bir kesim, kısıtlama içeren bir parametre segmenti olarak kabul edilir.
* Catch-all parametreleri en az özgüdür. Catch **-All** rotaları hakkında önemli bilgiler için bkz. [route Template başvurusu](#rtr) .

Tam değerler başvurusu için [GitHub 'da kaynak koda](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) bakın.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>URL oluşturma kavramları

URL oluşturma:

* , Yönlendirmenin bir yol değerleri kümesine göre bir URL yolu oluşturmalarına yönelik işlemdir.
* Uç noktalar ve bunlara erişen URL 'Ler arasında bir mantıksal ayrım sağlar.

Endpoint Routing <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 'yi içerir. `LinkGenerator` , [dı](xref:fundamentals/dependency-injection)tarafından kullanılabilen bir tek hizmettir. `LinkGenerator`API, yürütülen bir istek bağlamı dışında kullanılabilir. Etiket Yardımcıları, HTML Yardımcıları ve eylem sonuçları gibi ' i kullanan [Mvc. ıurlhelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) ve senaryoları <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> , [Action Results](xref:mvc/controllers/actions) [Tag Helpers](xref:mvc/views/tag-helpers/intro) `LinkGenerator` bağlantı oluşturma yetenekleri sağlamak için API 'yi dahili olarak kullanır.

Bağlantı Oluşturucu, bir **Adres** ve **Adres şemaları**kavramıyla desteklenir. Adres şeması, bağlantı oluşturma için göz önünde bulundurmanız gereken uç noktaları belirlemenin bir yoludur. Örneğin, yol adı ve yol değerleri senaryoları birçok kullanıcı, denetleyicilerden ve Razor sayfalardan bir adres düzeni olarak uygulanır.

Bağlantı Oluşturucu Razor aşağıdaki genişletme yöntemleri aracılığıyla denetleyicilere ve sayfalara bağlanabilir:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Bu yöntemlerin aşırı yüklemeleri, içeren bağımsız değişkenleri kabul eder `HttpContext` . Bu yöntemler, [URL. Action](xref:System.Web.Mvc.UrlHelper.Action*) ve [URL. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*)ile işlevsel olarak eşdeğerdir, ancak ek esneklik ve seçenekler sunar.

`GetPath*`Yöntemleri `Url.Action` `Url.Page` , ve ' de, mutlak bir yol içeren bir URI oluşturmak için en çok benzerdir. `GetUri*`Yöntemler her zaman bir düzen ve konak içeren mutlak BIR URI oluşturur. Bir öğesini kabul eden yöntemler, `HttpContext` yürütülmekte olan istek bağlamında BIR URI oluşturur. [Ortam](#ambient) yolu DEĞERLERI, URL taban yolu, şeması ve yürütülen istekten ana bilgisayar, geçersiz kılınmadıkça kullanılır.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> bir adresle çağırılır. URI oluşturma iki adımda gerçekleşir:

1. Adres, adresle eşleşen bir uç nokta listesine bağlanır.
1. Her uç nokta <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> , sağlanan değerlerle eşleşen bir yol deseninin bulunana kadar değerlendirilir. Elde edilen çıktı, bağlantı oluşturucuya sağlanan diğer URI parçalarıyla birleştirilir ve döndürülür.

Tarafından sunulan yöntemler, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> herhangi bir adres türü için standart bağlantı oluşturma yeteneklerini destekler. Bağlantı oluşturucuyu kullanmanın en kolay yolu, belirli bir adres türü için işlem gerçekleştiren genişletme yöntemlerine yöneliktir:

| Genişletme yöntemi | Açıklama |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Belirtilen değerleri temel alarak mutlak bir yola sahip bir URI oluşturur. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Belirtilen değerleri temel alarak mutlak bir URI oluşturur.             |

> [!WARNING]
> Çağırma yöntemlerinin aşağıdaki etkilerine dikkat edin <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * `GetUri*`Gelen isteklerin üstbilgisini doğrulayan bir uygulama yapılandırmasında uzantı yöntemlerini dikkatle kullanın `Host` . `Host`Gelen isteklerin üstbilgisi doğrulanmaz, güvenilir olmayan istek girişi bir görünüm veya sayfadaki URI 'ler içinde istemciye geri gönderilebilir. Tüm üretim uygulamalarının, `Host` üst bilgisini bilinen geçerli değerlere karşı doğrulamak için kendi sunucusunu yapılandırmasını öneririz.
>
> * <xref:Microsoft.AspNetCore.Routing.LinkGenerator>Veya ile birlikte ara yazılım içinde dikkatli kullanın `Map` `MapWhen` . `Map*` yürütülen isteğin temel yolunu değiştirir ve bu da bağlantı oluşturma çıktısını etkiler. Tüm <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 'ler temel yol belirtilmesine izin verir. Bağlantı oluşturma etkisini geri almak için boş bir temel yol belirtin `Map*` .

### <a name="middleware-example"></a>Ara yazılım örneği

Aşağıdaki örnekte, bir ara yazılım, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Mağaza ürünlerini listeleyen bir eylem yöntemine bağlantı oluşturmak için API 'yi kullanır. Ekleme tarafından bir sınıfa ve çağırarak bir `GenerateLink` uygulamadaki herhangi bir sınıf için kullanılabilir olan bağlantı oluşturucuyu kullanma:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Rota şablonu başvurusu

İçindeki belirteçler `{}` , yol eşleştirildiği takdirde bağlanan rota parametrelerini tanımlar. Yol segmentinde birden fazla yol parametresi tanımlanabilir, ancak yol parametreleri bir sabit değer ile ayrılmalıdır. Örneğin, `{controller=Home}{action=Index}` ve arasında değişmez değer olmadığından geçerli bir yol değil `{controller}` `{action}` .  Rota parametrelerinin bir adı olmalı ve ek öznitelikler belirtilmiş olabilir.

Yol parametrelerinden (örneğin, `{id}` ) ve yol ayırıcısından farklı bir metin, `/` URL içindeki metinle eşleşmelidir. Metin eşleştirme, büyük/küçük harfe duyarsız ve URL 'nin yolunun kodu çözülmüş gösterimine göre yapılır. Sabit bir yol parametresi sınırlayıcısından eşlemek `{` veya `}` karakteri tekrarlayarak sınırlayıcıdan kaçış. Örneğin `{{` veya `}}` .

Yıldız işareti `*` veya çift yıldız işareti `**` :

* URI 'nin geri kalanına bağlamak için bir yol parametresinin öneki olarak kullanılabilir.
* , **Catch-all** parametreleri olarak adlandırılır. Örneğin `blog/{**slug}` :
  * İle başlayan `/blog` ve bundan sonraki bir değere sahip tüm URI ile eşleşir.
  * Aşağıdaki değer, `/blog` [başlık](https://developer.mozilla.org/docs/Glossary/Slug) yolu değerine atanır.

[!INCLUDE[](~/includes/catchall.md)]

Catch-all parametreleri boş dizeyle de aynı olabilir.

Catch-all parametresi, yol ayırıcı karakterleri de dahil olmak üzere bir URL oluşturmak için kullanıldığında uygun karakterlerin bir kaçar `/` . Örneğin, `foo/{*path}` yol değerlerini içeren yol `{ path = "my/path" }` oluşturulur `foo/my%2Fpath` . Atlanan eğik çizgiye göz önünde edin. Yol ayırıcı karakterlerini yuvarlaklaştırmak için `**` Rota parametresi önekini kullanın. İle rota `foo/{**path}` `{ path = "my/path" }` oluşturulur `foo/my/path` .

İsteğe bağlı bir dosya uzantısına sahip bir dosya adı yakalamaya deneyen URL desenlerinin ek konuları vardır. Örneğin, şablonu göz önünde bulundurun `files/{filename}.{ext?}` . Hem hem de için değerler olduğunda `filename` `ext` her iki değer de doldurulur. URL 'de yalnızca bir değeri `filename` varsa, sondaki değer isteğe bağlı olduğundan yol eşleşir `.` . Aşağıdaki URL 'Ler bu rota ile eşleşiyor:

* `/files/myFile.txt`
* `/files/myFile`

Yol parametreleri, parametre adından sonra bir eşittir işaretiyle () ayırarak varsayılan değer belirtilerek belirlenmiş **varsayılan değerlere** sahip olabilir `=` . Örneğin, `{controller=Home}` `Home` için varsayılan değer olarak tanımlar `controller` . Parametresi için URL 'de hiçbir değer yoksa varsayılan değer kullanılır. Yol parametreleri, parametre adının sonuna bir soru işareti () eklenerek isteğe bağlı olarak yapılır `?` . Örneğin, `id?`. İsteğe bağlı değerler ve varsayılan yol parametreleri arasındaki fark şudur:

* Varsayılan değere sahip bir yol parametresi her zaman bir değer üretir.
* İsteğe bağlı bir parametre yalnızca istek URL 'SI tarafından bir değer sağlandığında bir değere sahip olur.

Rota parametrelerinin URL 'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamaları olabilir. `:`Yol parametre adından sonra ekleme ve kısıtlama adı, bir rota parametresinde bir satır içi kısıtlamayı belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, kısıtlama adından sonra parantez içine alınır `(...)` . Birden çok *satır içi kısıtlama* , başka bir `:` kısıtlama adı eklenerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL işlemede kullanılmak üzere bir örneği oluşturmak için hizmetine geçirilir. Örneğin, yol şablonu `blog/{article:minlength(10)}` `minlength` bağımsız değişkenle bir kısıtlama belirtir `10` . Yol kısıtlamaları ve Framework tarafından sunulan kısıtlamaların bir listesi hakkında daha fazla bilgi için, [route kısıtlama başvurusu](#route-constraint-reference) bölümüne bakın.

Rota parametrelerinin ayrıca parametre dönüştürücüler de olabilir. Parametre dönüştürücüler, URL 'Ler için bağlantılar ve eşleşen eylemler ve sayfalar oluştururken parametrenin değerini dönüştürür. Benzer kısıtlamalar, `:` yol parametre adından sonra bir ve transformatör adı eklenerek, parametre dönüştürücüleri bir rota parametresine satır içi eklenebilir. Örneğin, yol şablonu `blog/{article:slugify}` bir `slugify` transformatör belirtir. Parametre dönüştürücüler hakkında daha fazla bilgi için bkz. [Parameter transformatör başvurusu](#parameter-transformer-reference) bölümü.

Aşağıdaki tabloda örnek yol şablonları ve bunların davranışları gösterilmektedir:

| Rota şablonu                           | Örnek eşleşen URI    | İstek URI 'SI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Yalnızca tek bir yolla eşleşir `/hello` .                                     |
| `{Page=Home}`                            | `/`                     | İle eşleşir ve `Page` ayarlanır `Home` .                                         |
| `{Page=Home}`                            | `/Contact`              | İle eşleşir ve `Page` ayarlanır `Contact` .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products`Denetleyiciye ve `List` eyleme eşlenir.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` `Details` 123 olarak ayarlanan denetleyiciye ve eyleme eşlenir `id` . |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home`Denetleyiciye ve `Index` yönteme eşlenir. `id` yoksayılır.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | `Products`Denetleyiciye ve `Index` yönteme eşlenir. `id` yoksayılır.        |

Bir şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar, yol şablonu dışında da belirtilebilir.

### <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler, en çok [greksuz olmayan](#greedy) bir şekilde, sabit değer sınırlayıcılarını sağdan sola eşleştirerek işlenir. Örneğin, `[Route("/a{b}c{d}")]` karmaşık bir kesimdir.
Karmaşık segmentler, onları başarılı bir şekilde kullanmak için anlaşılması gereken belirli bir şekilde çalışır. Bu bölümdeki örnek, sınırlayıcı metnin parametre değerleri içinde görünmemesinin neden karmaşık segmentlerin gerçekten iyi şekilde çalıştığını gösterir. Bir [Regex](/dotnet/standard/base-types/regular-expressions) kullanarak ve daha karmaşık durumlar için değerleri el ile ayıklamanız gerekir.

[!INCLUDE[](~/includes/regex.md)]

Bu, yönlendirmenin şablon ve URL yolu ile gerçekleştirdiği adımların bir özetidir `/a{b}c{d}` `/abcd` . , `|` Algoritmanın nasıl çalıştığını görselleştirmeye yardımcı olmak için kullanılır:

* İlk sabit değer, sağdan sola `c` . Bu nedenle `/abcd` , doğru bir şekilde aranır ve bulur `/ab|c|d` .
* Right () öğesine ait her şey `d` artık Route parametresiyle eşleştirildi `{d}` .
* Sonraki değişmez değer, sağdan sola `a` . Bu nedenle `/ab|c|d` , ayrıldığımız yerden başlayarak arama yapılır `a` `/|a|b|c|d` .
* Right ( `b` ) değeri artık Route parametresiyle eşleştirildi `{b}` .
* Kalan bir metin yok ve kalan yol şablonu yok, bu nedenle bu bir eşleşmedir.

Aynı şablonu ve URL yolunu kullanarak negatif bir durum örneği aşağıda verilmiştir `/a{b}c{d}` `/aabcd` . , `|` Algoritmanın nasıl çalıştığını görselleştirmeye yardımcı olmak için kullanılır. Bu durum, aynı algoritma tarafından açıklanan bir eşleşme değildir:
* İlk sabit değer, sağdan sola `c` . Bu nedenle `/aabcd` , doğru bir şekilde aranır ve bulur `/aab|c|d` .
* Right () öğesine ait her şey `d` artık Route parametresiyle eşleştirildi `{d}` .
* Sonraki değişmez değer, sağdan sola `a` . Bu nedenle `/aab|c|d` , ayrıldığımız yerden başlayarak arama yapılır `a` `/a|a|b|c|d` .
* Right ( `b` ) değeri artık Route parametresiyle eşleştirildi `{b}` .
* Bu noktada, kalan metin vardır `a` ancak algoritmanın ayrıştırılacak yol şablonu kalmadı, bu nedenle bu bir eşleşme değildir.

Eşleşen algoritma [greyumsuz](#greedy)olmadığından:

* Her adımda mümkün olan en küçük metin miktarıyla eşleşir.
* Sınırlayıcı değerin parametre değerleri içinde göründüğü herhangi bir durum, eşleşmemelidir.

Normal ifadeler, eşleşen davranışları üzerinde çok daha fazla denetim sağlar.

<a name="greedy"></a>

Aynı zamanda [yavaş eşleşme](https://wikipedia.org/wiki/Regular_expression#Lazy_matching)olarak da bilinen Greedy eşleştirmesi, olası en büyük dizeyle eşleşir. Doyumsuz olmayan dize, olası en küçük dizeyle eşleşir.

## <a name="route-constraint-reference"></a>Yol kısıtlama başvurusu

Yol kısıtlamaları, gelen URL 'de bir eşleşme meydana geldiğinde ve URL yolu yol değerlerinde simgeleştirilir yürütülür. Yol kısıtlamaları genellikle yol şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda doğru veya yanlış bir karar vermez. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilmeyeceğini göz önünde bulundurmanız için yol değeri dışındaki verileri kullanır. Örneğin, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> bir ISTEğI http fiiline bağlı olarak kabul edebilir veya reddedebilir. Kısıtlamalar, yönlendirme isteklerinde ve bağlantı oluşturmada kullanılır.

> [!WARNING]
> Giriş doğrulaması için kısıtlamaları kullanmayın. Giriş doğrulaması için kısıtlamalar kullanılıyorsa, geçersiz giriş, bulunamayan bir Yanıt ile sonuçlanır `404` . Geçersiz giriş `400` , uygun bir hata iletisiyle hatalı bir istek üretmelidir. Yol kısıtlamaları, belirli bir rota için girdileri doğrulamak üzere değil, benzer yolların belirsizliğini ortadan kaldırmak için kullanılır.

Aşağıdaki tabloda örnek yol kısıtlamaları ve beklenen davranışlar gösterilmektedir:

| kısıtlama | Örnek | Örnek eşleşmeler | Notlar |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir tamsayıyla eşleşir |
| `bool` | `{active:bool}` | `true`, `FALSE` | Veya ile eşleşir `true` `false` . Büyük/küçük harf duyarsız |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | `DateTime`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | `decimal`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | `double`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | `float`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Geçerli bir `Guid` değerle eşleşir |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli bir `long` değerle eşleşir |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Dize en az 4 karakter olmalıdır |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Dize 8 karakterden uzun olmamalıdır |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Dize tam olarak 12 karakter uzunluğunda olmalıdır |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Dize en az 8 ve en fazla 16 karakter uzunluğunda olmalıdır |
| `min(value)` | `{age:min(18)}` | `19` | Tamsayı değeri en az 18 olmalıdır |
| `max(value)` | `{age:max(120)}` | `91` | Tamsayı değeri 120 ' ten fazla olmamalıdır |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Tamsayı değeri en az 18 olmalı ancak 120 ' ten fazla olmamalıdır |
| `alpha` | `{name:alpha}` | `Rick` | Dize bir veya daha fazla alfabetik karakterden oluşmalıdır `a` - `z` ve büyük/küçük harfe duyarsız olmalıdır. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Dize, normal ifadeyle eşleşmelidir. Normal ifade tanımlama hakkında ipuçlarına bakın. |
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre olmayan bir değerin mevcut olduğunu zorlamak için kullanılır |

[!INCLUDE[](~/includes/regex.md)]

Birden çok, iki nokta üst üste sınırlı kısıtlama, tek bir parametreye uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle kısıtlar:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL 'YI doğrulayan ve CLR türüne dönüştürülen yol kısıtlamaları her zaman sabit kültürü kullanır. Örneğin, CLR türüne dönüştürme `int` veya `DateTime` . Bu kısıtlamalar, URL 'nin yerelleştirilebilir olmadığını varsayar. Framework tarafından sunulan yol kısıtlamaları, yol değerlerinde depolanan değerleri değiştirmez. URL 'den Ayrıştırılan tüm rota değerleri dizeler olarak depolanır. Örneğin, `float` kısıtlama yol değerini bir float öğesine dönüştürmeye çalışır, ancak dönüştürülen değer yalnızca bir float öğesine dönüştürülebileceğini doğrulamak için kullanılır.

### <a name="regular-expressions-in-constraints"></a>Kısıtlamalarda normal ifadeler

[!INCLUDE[](~/includes/regex.md)]

Normal ifadeler, Route kısıtlaması kullanılarak satır içi kısıtlamalar olarak belirtilebilir `regex(...)` . <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>Ailesindeki Yöntemler ayrıca Kısıtlamaların bir nesne sabit değerini de kabul eder. Bu form kullanılıyorsa, dize değerleri normal ifadeler olarak yorumlanır.

Aşağıdaki kod, bir satır içi Regex kısıtlaması kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Aşağıdaki kod, bir Regex kısıtlaması belirtmek için bir nesne değişmez değeri kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

ASP.NET Core Framework, `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` normal ifade oluşturucusuna ekler. <xref:System.Text.RegularExpressions.RegexOptions>Bu üyelerin açıklaması için bkz..

Normal ifadeler, Yönlendirme ve C# dili tarafından kullanılanlarla benzerlik olan sınırlayıcıları ve belirteçleri kullanır. Normal ifade belirteçlerinin atlanmalıdır. Normal ifadeyi `^\d{3}-\d{2}-\d{4}$` bir satır içi kısıtlamada kullanmak için aşağıdakilerden birini kullanın:

* Dizede `\` belirtilen karakterleri, `\\` dize kaçış karakterinden kaçınmak Için C# kaynak dosyasındaki karakter olarak değiştirin `\` .
* Tam [dize sabit değerleri](/dotnet/csharp/language-reference/keywords/string).

Yönlendirme parametresi sınırlayıcı karakterlerini `{` ,,, `}` , `[` `]` ifadedeki karakterleri çift kaçış, örneğin `{{` `}}` `[[` `]]` ,,,,. Aşağıdaki tabloda, bir normal ifade ve bunun kaçış sürümü gösterilmektedir:

| Normal ifade    | Kaçan normal ifade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmelerde kullanılan normal ifadeler, genellikle karakteriyle başlar `^` ve dizenin başlangıç konumuyla eşleşir. İfadeler genellikle `$` karakteriyle biter ve dizenin sonuyla eşleşir. `^`Ve `$` karakterleri, normal ifadenin tüm yol parametresi değeri ile eşleştiğinden emin olun. `^`Ve karakterleri olmadan `$` normal ifade, dize içindeki herhangi bir alt dizeden eşleşir ve bu genellikle istenmeyen bir ifadedir. Aşağıdaki tabloda örnekler verilmektedir ve bunların eşleşmesinin neden eşleşmediği veya eşleşmemesi açıklanmaktadır:

| Expression   | Dize    | Eşleştirme | Yorum               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | MZ        | Yes   | Eşleşen ifadesi    |
| `[a-z]{2}`   | MZ        | Yes   | Büyük/küçük harfe duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | Bkz. `^` ve `$` üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | Bkz. `^` ve `$` üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için bkz. [.NET Framework normal ifadeler](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için, normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` yalnızca `action` Rota değeri `list` ,, veya ile eşleşir `get` `create` . Kısıtlama sözlüğüne geçirilirse dize `^(list|get|create)$` eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyen kısıtlama sözlüğünde geçirilen kısıtlamalar, normal ifadeler olarak da değerlendirilir. Bilinen kısıtlamaların biriyle eşleşmeyen bir şablon içinde geçirilen kısıtlamalar normal ifadeler olarak değerlendirilmez.

### <a name="custom-route-constraints"></a>Özel yol kısıtlamaları

Özel yol kısıtlamaları arabirimi uygulayarak oluşturulabilir <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> . `IRouteConstraint`Arabirimi, <xref:System.Web.Routing.IRouteConstraint.Match*> `true` kısıtlama karşılanıp Aksi takdirde döndüren ' i içerir `false` .

Özel yol kısıtlamaları nadiren gereklidir. Özel bir yol kısıtlaması uygulamadan önce, model bağlama gibi alternatifleri göz önünde bulundurun.

ASP.NET Core [kısıtlamalar](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) klasörü, bir kısıtlama oluşturmaya yönelik iyi örnekler sağlar. Örneğin, [Guidrouteconstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).

Özel bir kullanmak için `IRouteConstraint` yol kısıtlama türü, uygulamanın hizmet kapsayıcısında kayıtlı olması gerekir <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> . `ConstraintMap`, Yol kısıtlama anahtarlarını `IRouteConstraint` Bu kısıtlamaları doğrulayan uygulamalarla eşleyen bir sözlüktür. Bir uygulama `ConstraintMap` , `Startup.ConfigureServices` hizmetlerin bir parçası olarak ' de güncelleştirilir [. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) çağrısı veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan ile yapılandırma `services.Configure<RouteOptions>` . Örneğin:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Önceki kısıtlama aşağıdaki kodda uygulanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Uygulamasının uygulanması, `MyCustomConstraint` `0` bir rota parametresine uygulanmasını engeller:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Yukarıdaki kod:

* `0` `{id}` Yolun segmentinde engeller.
* , Özel bir kısıtlamayı uygulamaya yönelik temel bir örnek sağlamak için gösterilmektedir. Bu, bir üretim uygulamasında kullanılmamalıdır.

Aşağıdaki kod, içeren bir öğenin işlenmesini önlemek için daha iyi bir yaklaşımdır `id` `0` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Yukarıdaki kodda yaklaşım üzerinde aşağıdaki avantajlar bulunur `MyCustomConstraint` :

* Özel bir kısıtlama gerektirmez.
* Yol parametresi içerdiğinde daha açıklayıcı bir hata döndürür `0` .

## <a name="parameter-transformer-reference"></a>Parametre transformatörü başvurusu

Parametre dönüştürücüler:

* Kullanarak bağlantı oluştururken yürütün <xref:Microsoft.AspNetCore.Routing.LinkGenerator> .
* Uygulayın <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName> .
* Kullanılarak yapılandırılır <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> .
* Parametrenin yol değerini alın ve yeni bir dize değerine dönüştürün.
* Oluşturulan bağlantıda Dönüştürülmüş değerin kullanılmasına neden olacak.

Örneğin, yol düzeninde bir özel `slugify` parametre transformatörü `blog\{article:slugify}` `Url.Action(new { article = "MyTestArticle" })` oluşturur `blog\my-test-article` .

Aşağıdaki uygulamayı göz önünde bulundurun `IOutboundParameterTransformer` :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Bir parametre transformatörü bir yol düzeninde kullanmak için, şunu içinde kullanarak yapılandırın <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

ASP.NET Core Framework, bir uç noktanın çözümlediği URI 'yi dönüştürmek için parametre dönüştürücüler kullanır. Örneğin, parametre dönüştürücüler,,, ve ile eşleştirmek için kullanılan rota değerlerini dönüştürür `area` `controller` `action` `page` .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Önceki yol şablonuyla, eylem `SubscriptionManagementController.GetAll` URI ile eşleştirilir `/subscription-management/get-all` . Bir parametre transformatörü bir bağlantı oluşturmak için kullanılan rota değerlerini değiştirmez. Örneğin, `Url.Action("GetAll", "SubscriptionManagement")` çıktılar `/subscription-management/get-all` .

ASP.NET Core, oluşturulan yollarla parametre dönüştürücüler kullanmak için API kuralları sağlar:

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>MVC kuralı, uygulamadaki tüm öznitelik yollarına belirtilen bir parametre transformatörü uygular. Parametre transformatörü, öznitelik yol belirteçlerini değiştirildiklerinde dönüştürür. Daha fazla bilgi için bkz. [belirteç değişimini özelleştirmek için bir parametre transformatörü kullanma](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Sayfalar <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API kuralını kullanır. Bu kural, belirtilen bir parametre transformatörü otomatik olarak bulunan tüm Razor sayfalara uygular. Parametre transformatörü, sayfa yollarının klasör ve dosya adı segmentlerini dönüştürür Razor . Daha fazla bilgi için bkz. [sayfa yollarını özelleştirmek için bir parametre transformatörü kullanma](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Bu bölüm, URL oluşturma tarafından uygulanan algoritmanın başvurusunu içerir. Uygulamada, URL oluşturma 'nın çoğu karmaşık örneği denetleyicileri veya sayfaları kullanır Razor . Daha fazla bilgi için bkz.  [denetleyicilerde yönlendirme](xref:mvc/controllers/routing) .

URL oluşturma işlemi [Linkgenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) veya benzer bir yöntem çağrısıyla başlar. Yöntemi bir adres, bir rota değerleri kümesi ve isteğe bağlı olarak geçerli istek hakkında bilgi ile birlikte sağlanır `HttpContext` .

İlk adım, adresin türüyle eşleşen bir aday uç nokta kümesini çözümlemek için adresi kullanmaktır [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) .

Adres kümesi tarafından bir aday kümesini bulduktan sonra, uç noktalar bir URL oluşturma işlemi başarılı olana kadar sıralanır ve işlenir. URL oluşturma belirsizlikleri için Denetim **yapmaz** , döndürülen ilk sonuç son sonuçdur.

### <a name="troubleshooting-url-generation-with-logging"></a>Günlüğe kaydetme ile URL oluşturma sorunlarını giderme

URL oluşturma sorunlarını gidermeye yönelik ilk adım, günlük düzeyini `Microsoft.AspNetCore.Routing` olarak ayarlıyor `TRACE` . `LinkGenerator` işleme hakkında birçok ayrıntıyı günlüğe kaydeder ve bu da sorunları gidermek için yararlı olabilir.

URL oluşturma hakkında ayrıntılı bilgi için bkz. [URL oluşturma başvurusu](#ugr) .

### <a name="addresses"></a>Adresler

Adresler, bağlantı oluşturucuya bir çağrıyı bir aday uç noktası kümesine bağlamak için kullanılan URL oluşturma kavramıdır.

Adresler, varsayılan olarak iki uygulama ile birlikte gelen Genişletilebilir bir kavramdır:

* Şu adres olarak *uç nokta adı* ( `string` ) kullanılıyor:
    * MVC 'nin yol adına benzer işlevsellik sağlar.
    * <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata>Meta veri türünü kullanır.
    * Belirtilen dizeyi tüm kayıtlı uç noktaların meta verilerinde çözer.
    * Birden fazla uç nokta aynı adı kullanıyorsa, başlangıçta bir özel durum oluşturur.
    * Denetleyicilerin ve sayfaların dışında genel amaçlı kullanım için önerilir Razor .
* Adres olarak *yol değerlerini* ( <xref:Microsoft.AspNetCore.Routing.RouteValuesAddress> ) kullanma:
    * Denetleyiciler ve Razor sayfalar için eskı URL oluşturmaya benzer işlevler sağlar.
    * Genişletmek ve hata ayıklamak için çok karmaşık.
    * `IUrlHelper`, Etiket yardımcıları, HTML Yardımcıları, eylem sonuçları vb. tarafından kullanılan uygulamayı sağlar.

Adres şemasının rolü, rastgele ölçütlere göre adres ve eşleşen uç noktalar arasındaki ilişkiyi oluşturmak için kullanılır:

* Uç nokta adı şeması, temel bir sözlük araması gerçekleştirir.
* Yol değerleri şeması, set algoritmasının karmaşık bir en iyi alt kümesine sahiptir.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Çevresel değerler ve açık değerler

Yönlendirme geçerli istekten, geçerli isteğin rota değerlerine erişir `HttpContext.Request.RouteValues` . Geçerli istekle ilişkili değerler **çevresel değerler**olarak adlandırılır. Netlik amacı doğrultusunda, belgeler yöntemlere geçirilen yol değerlerini **açık değerler**olarak ifade eder.

Aşağıdaki örnek, ortam değerlerini ve açık değerleri gösterir. Geçerli istekten ve açık değerlerden çevresel değerler sağlar: `{ id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Yukarıdaki kod:

* Döndürdüğü `/Widget/Index/17`
* <xref:Microsoft.AspNetCore.Routing.LinkGenerator> [Dı](xref:fundamentals/dependency-injection)aracılığıyla alır.

Aşağıdaki kod hiçbir çevresel değer ve açık değer sağlar: `{ controller = "Home", action = "Subscribe", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Önceki yöntem şunu döndürür `/Home/Subscribe/17`

Aşağıdaki kod `WidgetController` döndürmektedir `/Widget/Subscribe/17` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Aşağıdaki kod, denetleyiciyi geçerli istekteki çevresel değerlerden ve açık değerlerde sağlar: `{ action = "Edit", id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Yukarıdaki kodda:

* `/Gadget/Edit/17` döndürülür.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> alır <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> .
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
bir eylem yöntemi için mutlak bir yol içeren bir URL oluşturur. URL, belirtilen `action` adı ve değerleri içerir `route` .

Aşağıdaki kod, geçerli istekten ve açık değerlerden çevresel değerler sağlar: `{ page = "./Edit, id = 17, }` :

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Önceki kod, `url`  `/Edit/17` düzenleme Razor sayfası aşağıdaki sayfa yönergesini içerdiğinde olarak ayarlanır:

 `@page "{id:int}"`

Düzenleme sayfasında `"{id:int}"` yol şablonu yoksa, `url` olur `/Edit?id=17` .

MVC 'nin davranışı, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> burada açıklanan kurallara ek olarak bir karmaşıklık katmanı ekler:

* `IUrlHelper` her zaman geçerli istekten çevresel değerler olarak yol değerlerini sağlar.
* [Iurlhelper. Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) , `action` `controller` Geliştirici tarafından geçersiz kılınmadığı sürece her zaman geçerli ve yol değerlerini açık değerler olarak kopyalar.
* [Iurlhelper. Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) , `page` geçersiz kılınmadığı sürece geçerli yol değerini açık bir değer olarak kopyalar. <!--by the user-->
* `IUrlHelper.Page` her zaman geçersiz `handler` `null` kılınmadığı sürece açık değerler olarak geçerli yol değerini geçersiz kılar.

MVC kendi kurallarını takip ettiğinden, kullanıcılar genellikle çevresel değerlerinin davranış ayrıntılarına göre görünür. Geçmiş ve uyumluluk nedenleriyle,,, ve gibi belirli rota `action` değerlerinin `controller` `page` `handler` kendi özel durum davranışları vardır.

Tarafından sunulan eşdeğer işlevsellik `LinkGenerator.GetPathByAction` ve `LinkGenerator.GetPathByPage` Bu bozuklukları `IUrlHelper` uyumluluk açısından çoğaltır.

### <a name="url-generation-process"></a>URL oluşturma işlemi

Aday uç noktalar kümesi bulunduğunda, URL oluşturma algoritması:

* Son noktaları yinelemeli olarak işler.
* İlk başarılı sonucu döndürür.

Bu işlemdeki ilk adıma **Rota değeri ınvalidation**adı verilir.  Rota değeri geçersiz kılma, yönlendirmenin çevresel değerlerden hangi rota değerlerinin kullanılması gerektiğini ve göz ardı edileceğine yönelik bir işlemdir. Her ortam değeri, açık değerlerle birlikte değerlendirilir ve yok sayılır.

Ortam değerlerinin rolünü düşünmenin en iyi yolu, bazı yaygın durumlarda, yazma uygulama geliştiricilerinin kaydedilmesini denedikleri değerlerdir. Geleneksel olarak, ortam değerlerinin yararlı olduğu senaryolar MVC ile ilgilidir:

* Aynı denetleyicideki başka bir eyleme bağlantı sırasında, denetleyicinin adının belirtilmesi gerekmez.
* Aynı alandaki başka bir denetleyiciye bağlanırken, alan adının belirtilmesi gerekmez.
* Aynı eylem yöntemine bağlanırken, rota değerlerinin belirtilmesi gerekmez.
* Uygulamanın başka bir bölümüne bağlanırken, uygulamanın o bölümünde anlamı olmayan rota değerlerini yürütmek istemezsiniz.

Ya da `LinkGenerator` `IUrlHelper` Bu dönüşe yapılan çağrılar `null` genellikle yol değeri doğrulaması anlaşılmadığında oluşur. Yolun sorunu çözüp çözmediğini görmek için daha fazla yol değeri belirtip yol değerlerini geçersiz kılma sorunlarını giderin.

Rota değeri geçersiz kılma, uygulamanın URL şeması, soldan sağa doğru oluşturulmuş bir hiyerarşi ile hiyerarşik olduğu varsayımıyla birlikte çalışıyor. `{controller}/{action}/{id?}`Bu uygulamada nasıl çalıştığını daha kolay bir şekilde öğrenmek için temel denetleyici yönlendirme şablonunu göz önünde bulundurun. Bir değer **değişikliği** , sağda görünen tüm rota değerlerini **geçersiz kılar** . Bu, hiyerarşiyle ilgili varsayımını yansıtır. Uygulamanın için bir ortam değeri varsa `id` ve işlem için farklı bir değer belirtiyorsa `controller` :

* `id``{controller}`, öğesinin solunda olduğu için yeniden kullanılamaz `{id?}` .

Bu ilkeyi gösteren bazı örnekler:

* Açık değerler için bir değer içeriyorsa `id` , ortam değeri `id` yok sayılır. Ve için çevresel değerler `controller` `action` kullanılabilir.
* Açık değerler için bir değer içeriyorsa `action` , için herhangi bir ortam değeri `action` yok sayılır. İçin çevresel değerler `controller` kullanılabilir. İçin açık değeri, `action` için çevresel değerden farklıysa `action` , `id` değer kullanılmaz.  İçin açık değeri, `action` için çevresel değer ile aynıysa `action` , `id` değer kullanılabilir.
* Açık değerler için bir değer içeriyorsa `controller` , için herhangi bir ortam değeri `controller` yok sayılır. İçin açık değeri, `controller` için çevresel değerden farklıysa `controller` , `action` ve `id` değerleri kullanılmaz. İçin açık değeri, `controller` için çevresel değer ile aynıysa `controller` , `action` ve `id` değerleri kullanılabilir.

Bu işlem, öznitelik yollarının ve adanmış geleneksel yolların varlığı tarafından daha karmaşıktır. `{controller}/{action}/{id?}`Yönlendirme parametrelerini kullanarak bir hiyerarşiyi belirtmek gibi denetleyici geleneksel yolları. [Özel geleneksel yollar](xref:mvc/controllers/routing#dcr) ve denetleyicilere ve sayfalara yönelik [öznitelik yolları](xref:mvc/controllers/routing#ar) için Razor :

* Yol değerlerinin bir hiyerarşisi vardır.
* Bunlar şablonda görünmez.

Bu gibi durumlarda, URL oluşturma **gerekli değerler** kavramını tanımlar. Denetleyiciler ve sayfalar tarafından oluşturulan uç noktalar, Razor Rota değeri geçersiz kılma çalışmasına izin veren gerekli değerlere sahiptir.

Ayrıntı olarak yol değeri geçersiz kılma algoritması:

* Gerekli değer adları, yol parametreleriyle birleştirilir ve soldan sağa işlenir.
* Her parametre için çevresel değer ve açık değer karşılaştırılır:
    * Çevresel değer ve açık değer aynıysa işlem devam eder.
    * Çevresel değer varsa ve açık değer değilse, URL oluşturulurken çevresel değer kullanılır.
    * Çevresel değer yoksa ve açık değer ise, ortam değerini ve sonraki tüm ortam değerlerini reddedin.
    * Çevresel değer ve açık değer varsa ve iki değer farklıysa, ortam değerini ve sonraki tüm ortam değerlerini reddedin.

Bu noktada, URL oluşturma işlemi yol kısıtlamalarını değerlendirmek için hazırlayın. Kabul edilen değerler kümesi, kısıtlamalara sunulan parametre varsayılan değerleriyle birleştirilir. Kısıtlamaların tümü başarılı olursa işlem devam eder.

Ardından, **kabul edilen değerler** yol şablonunu genişletmek için kullanılabilir. Yol şablonu işlenir:

* Soldan sağa.
* Her parametrenin kabul edilen değeri değiştirildi.
* Aşağıdaki özel durumlar ile:
  * Kabul edilen değerlerde bir değer eksikse ve parametrenin varsayılan değeri varsa, varsayılan değer kullanılır.
  * Kabul edilen değerlerde bir değer eksikse ve parametresi isteğe bağlıdır, işleme devam eder.
  * Eksik bir isteğe bağlı parametrenin sağında herhangi bir rota parametresinin değeri varsa, işlem başarısız olur.
  * <!-- review default-valued parameters optional parameters --> Ardışık varsayılan değerli parametreler ve isteğe bağlı parametreler mümkün olduğunda daraltılır.

Yolun bir segmentiyle eşleşmeyen değerler sorgu dizesine eklenir. Aşağıdaki tabloda, yol şablonu kullanılırken sonuç gösterilmektedir `{controller}/{action}/{id?}` .

| Çevresel değerler                     | Açık değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "giriş"                | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | denetleyici = "Order", Action = "hakkında" | `/Order/About`          |
| denetleyici = "giriş", renk = "kırmızı" | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | Action = "hakkında", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Rota değeri geçersiz kılma sorunları

ASP.NET Core 3,0 itibariyle, önceki ASP.NET Core sürümlerde kullanılan bazı URL oluşturma şemaları, URL oluşturma ile iyi çalışmaz. ASP.NET Core takım, gelecekteki sürümlerde bu ihtiyaçları karşılamak için özellik eklemeyi planlıyor. Şimdilik en iyi çözüm eski yönlendirmeyi kullanmaktır.

Aşağıdaki kod, yönlendirme tarafından desteklenmeyen bir URL oluşturma şemasına bir örnek gösterir.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

Yukarıdaki kodda, `culture` Yerelleştirme için Route parametresi kullanılır. Bunun için `culture` parametre her zaman bir çevresel değer olarak kabul edilir. Ancak, `culture` gerekli değerlerin çalışma şekli nedeniyle parametre bir çevresel değer olarak kabul edilmez:

* `"default"`Yol şablonunda, `culture` yol parametresi solunda bulunur `controller` , bu nedenle değişiklikler `controller` geçersiz olmaz `culture` .
* `"blog"`Yol şablonunda, `culture` yol parametresi, `controller` gerekli değerlerde görüntülenen, öğesinin sağında kabul edilir.

## <a name="configuring-endpoint-metadata"></a>Uç nokta meta verilerini yapılandırma

Aşağıdaki bağlantılar, uç nokta meta verilerini yapılandırma hakkında bilgi sağlar:

* [Uç nokta yönlendirme ile CORS 'yi etkinleştirme](xref:security/cors#enable-cors-with-endpoint-routing)
* Özel bir öznitelik kullanan [ıauthorizationpolicyprovider örneği](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) `[MinimumAgeAuthorize]`
* [[Yetkilendir] özniteliğiyle test kimlik doğrulaması](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [[Yetkilendir] özniteliğiyle düzeni seçme](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [[Yetkilendir] özniteliğini kullanarak ilke uygulama](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>RequireHost ile yollarla eşleşen ana bilgisayar

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> rotaya belirtilen Konağı gerektiren bir kısıtlama uygular. `RequireHost`Veya [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parametresi şu olabilir:

* Ana bilgisayar: `www.domain.com` , `www.domain.com` herhangi bir bağlantı noktasıyla eşleşir.
* Joker karakterle,, `*.domain.com` `www.domain.com` `subdomain.domain.com` veya `www.subdomain.domain.com` herhangi bir bağlantı noktasında eşleşen ana bilgisayar.
* Bağlantı noktası: `*:5000` , bağlantı noktası 5000 ile herhangi bir konak ile eşleşir.
* Konak ve bağlantı noktası: `www.domain.com:5000` veya `*.domain.com:5000` , ana bilgisayar ve bağlantı noktasıyla eşleşir.

Veya kullanılarak birden çok parametre belirtilebilir `RequireHost` `[Host]` . Kısıtlama, parametrelerden herhangi biri için geçerli olan konaklarla eşleşir. Örneğin,, `[Host("domain.com", "*.domain.com")]` ve ile eşleşir `domain.com` `www.domain.com` `subdomain.domain.com` .

Aşağıdaki kod, `RequireHost` rotada belirtilen Konağı gerektirmek için kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Aşağıdaki kod, `[Host]` belirtilen konaklardan herhangi birini gerektirmek için denetleyicideki özniteliğini kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

`[Host]`Özniteliği hem denetleyici hem de eylem yöntemine uygulandığında:

* Eylem üzerindeki özniteliği kullanılır.
* Denetleyici özniteliği yok sayılır.

## <a name="performance-guidance-for-routing"></a>Yönlendirme için performans Kılavuzu

Yönlendirmenin çoğu, performansı artırmak için ASP.NET Core 3,0 ' de güncelleştirildi.

Bir uygulamada performans sorunları olduğunda, yönlendirme genellikle sorun olarak şüpheli. Yönlendirme nedeni, denetleyiciler ve sayfalar gibi çerçevelerin, Razor kendi günlük iletilerinde çerçeve içinde harcanan süreyi raporlamadır. Denetleyiciler tarafından bildirilen zaman ve isteğin toplam süresi arasında önemli bir fark olduğunda:

* Geliştiriciler, uygulamanın kodunu sorunun kaynağı olarak ortadan kaldırır.
* Yönlendirmenin neden olduğunu varsaymak yaygındır.

Yönlendirme, binlerce uç nokta kullanılarak performansa göre test edilir. Tipik bir uygulamanın çok büyük bir performans sorunuyla karşılaşmasının pek olası olması normaldir. Yavaş yönlendirme performansının en yaygın temel nedenlerinden biri genellikle hatalı davranan özel bir ara yazılım olur.

Aşağıdaki kod örneğinde, gecikme kaynağını daraltmak için temel bir teknik gösterilmektedir:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Zaman yönlendirme:

* Yukarıdaki kodda gösterilen zamanlama ara yazılımı kopyasıyla her bir ara yazılımı ayırma.
* Zamanlama verilerinin kodla ilişkilendirilmesi için benzersiz bir tanımlayıcı ekleyin.

Bu, örneğin, çok önemli olduğunda gecikmeyi daraltmak için temel bir yoldur `10ms` .  `Time 2` `Time 1` Raporlar, ara yazılım içinde harcanan süreyi çıkararak `UseRouting` .

Aşağıdaki kod, önceki zamanlama koduna yönelik daha küçük bir yaklaşım kullanır:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potansiyel olarak pahalı yönlendirme özellikleri

Aşağıdaki listede, temel yol şablonlarıyla karşılaştırıldığında görece maliyetli yönlendirme özellikleri hakkında bazı Öngörüler sunulmaktadır:

* Normal ifadeler: karmaşık ifadeler yazmak veya az miktarda giriş ile uzun süre çalışma süresi olması mümkündür.

* Karmaşık segmentler ( `{x}-{y}-{z}` ): 
  * , Normal bir URL yolu segmentini ayrıştırmaktan önemli ölçüde daha pahalıdır.
  * Daha fazla sayıda alt dizenin ayrılması sonucunu elde edin.
  * Karmaşık kesim mantığı ASP.NET Core 3,0 yönlendirme performansı güncelleştirmesinde güncelleştirilmedi.

* Zaman uyumlu veri erişimi: birçok karmaşık uygulamanın, yönlendirmenin bir parçası olarak veritabanı erişimi vardır. ASP.NET Core 2,2 ve önceki yönlendirme, veritabanı erişim yönlendirmesini desteklemek için doğru genişletilebilirlik noktaları sağlamayabilir. Örneğin, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , ve <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> zaman uyumludur. Ve gibi genişletilebilirlik noktaları <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> zaman uyumsuzdur.

## <a name="guidance-for-library-authors"></a>Kitaplık yazarları için kılavuz

Bu bölüm, yönlendirmenin üzerine binen üst düzey kitaplık yazarları için yönergeler içerir. Bu ayrıntılar, uygulama geliştiricilerinin yönlendirmeyi genişleten kitaplıkları ve çerçeveleri kullanarak iyi bir deneyim aldığından emin olmak için tasarlanmıştır.

### <a name="define-endpoints"></a>Uç noktaları tanımlama

URL eşleştirmesi için yönlendirmeyi kullanan bir çerçeve oluşturmak için, en üstünde yapı yapan bir kullanıcı deneyimi tanımlayarak başlayın <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> .

Üzerine derleme **yapın** <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> . Bu, kullanıcıların kendi çatısını diğer ASP.NET Core özellikleriyle karışıklık olmadan oluşturma olanağı sağlar. Her ASP.NET Core şablonu yönlendirme içerir. Yönlendirmenin mevcut olduğunu ve kullanıcılara tanıdık olduğunu varsayın.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

Bu, uygulayan bir çağrıdan Sealed somut **tür döndürün** `MapMyFramework(...)` <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> . Çoğu Framework `Map...` yöntemi bu kalıbı izler. `IEndpointConventionBuilder`Arabirim:

* Meta verilerin bir kısmını bilme olanağı tanır.
* , Çeşitli uzantı yöntemleriyle hedeflenmelidir.

Kendi türünü bildirmek, çerçeveye özgü işlevselliği oluşturucuya eklemenize olanak tanır. Çerçeve tarafından tanımlanan bir oluşturucuyu sarmadan ve çağrıları iletecek.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**CONSIDER** Kendinizinkini yazmayı düşünün <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> . `EndpointDataSource` , uç nokta koleksiyonunu bildirmek ve güncelleştirmek için alt düzey temel değer. `EndpointDataSource` , denetleyiciler ve sayfalar tarafından kullanılan güçlü bir API 'dir Razor .

Yönlendirme testlerinin, güncelleştirme olmayan bir veri kaynağına ilişkin [temel bir örneği](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) vardır.

Varsayılan olarak **kaydetmeyi denemeyin** `EndpointDataSource` . Kullanıcıların çatısını kaydetmesini gerektir <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> . Yönlendirmenin felseı, varsayılan olarak hiçbir şey dahil değildir ve `UseEndpoints` uç noktaların kaydedileceği yerdir.

### <a name="creating-routing-integrated-middleware"></a>Yönlendirme ile tümleşik ara yazılım oluşturma

Meta veri türlerini arabirim olarak tanımlamayı **düşünün** .

Meta veri türlerini sınıflar ve yöntemlerde öznitelik olarak kullanmayı **mümkün hale getirir** .

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Denetleyiciler ve sayfalar gibi çerçeveler Razor , meta veri özniteliklerinin türlere ve yöntemlere uygulanmasını destekler. Meta veri türleri bildirirseniz:

* Bunları [öznitelik](/dotnet/csharp/programming-guide/concepts/attributes/)olarak erişilebilir yapın.
* Çoğu Kullanıcı öznitelikleri uygulamayla tanıdık gelecektir.

Bir arabirim olarak bir meta veri türü bildirmek, başka bir esneklik katmanı ekler:

* Arabirimler birleştirilebilir.
* Geliştiriciler, birden çok ilkeyi birleştiren kendi türlerini bildirebilir.

Aşağıdaki örnekte gösterildiği gibi meta verileri **geçersiz kılmak mümkün hale gelir** :

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Bu yönergeleri izlemek için en iyi yol, **işaretleyici meta verileri**tanımlamayı önleyemaktır:

* Yalnızca meta veri türünün varolup olmadığını göz atın.
* Meta verilerde bir özellik tanımlayın ve özelliği denetleyin.

Meta veri koleksiyonu sıralanmıştır ve önceliğe göre geçersiz kılmayı destekler. Denetleyiciler söz konusu olduğunda, eylem yöntemindeki meta veriler en özeldir.

Ara yazılımı, yönlendirme olmadan ve ile **yararlı hale getirin** .

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

Bu yönergeye örnek olarak, ara yazılımı göz önünde bulundurun `UseAuthorization` . Yetkilendirme ara yazılımı, bir geri dönüş ilkesi geçirmenize olanak sağlar. <!-- shown where?  (shown here) --> Belirtilmişse, geri dönüş ilkesi her ikisi için de geçerlidir:

* Belirli bir ilke olmadan uç noktalar.
* Bir uç noktasıyla eşleşmeyen istekler.

Bu, yetkilendirme ara yazılımını yönlendirme bağlamı dışında yararlı hale getirir. Yetkilendirme ara yazılımı geleneksel ara yazılım programlama için kullanılabilir.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Yönlendirme, istek URI 'Lerini uç noktalarla eşleştirmekten ve gelen istekleri bu uç noktalara gönderen sorumludur. Yollar uygulamada tanımlanır ve uygulama başlatıldığında yapılandırılır. Yol, isteğe bağlı olarak istekte bulunan URL 'den değerleri ayıklayabilir ve bu değerler, istek işleme için kullanılabilir. Uygulamadan yönlendirme bilgilerini kullanarak, yönlendirme, uç noktalarıyla eşlenen URL 'Ler de oluşturabilir.

ASP.NET Core 2,2 ' de en son yönlendirme senaryolarını kullanmak için, ' de MVC Hizmetleri kaydının [Uyumluluk sürümünü](xref:mvc/compatibility-version) belirtin `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Bu <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> seçenek yönlendirmenin <xref:Microsoft.AspNetCore.Routing.IRouter> ASP.NET Core 2,1 veya önceki bir sürümünün uç nokta tabanlı Logic mi yoksa tabanlı mantığını mı kullanması gerektiğini belirler. Uyumluluk sürümü 2,2 veya üzeri bir değere ayarlandığında, varsayılan değer olur `true` . `false`Önceki yönlendirme mantığını kullanmak için değeri olarak ayarlayın:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Tabanlı yönlendirme hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Routing.IRouter> [bu konunun ASP.NET Core 2,1 sürümüne](?view=aspnetcore-2.1)bakın.

> [!IMPORTANT]
> Bu belge, alt düzey ASP.NET Core yönlendirmeyi içerir. ASP.NET Core MVC yönlendirme hakkında bilgi için bkz <xref:mvc/controllers/routing> .. Sayfalardaki yönlendirme kuralları hakkında daha fazla bilgi için Razor bkz <xref:razor-pages/razor-pages-conventions> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Yönlendirme temelleri

Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}` :

* Temel ve açıklayıcı bir yönlendirme düzenini destekler.
* , UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.

Geliştiriciler, genellikle [öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing) veya adanmış geleneksel yollar kullanarak özel durumlarda uygulamanın yüksek trafikte daha fazla terse yolu ekler. Özelleştirilmiş durumlar örnekleri, blog ve eticaret uç noktaları içerir.

Web API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır. Bu, örneğin, al ve POST gibi birçok işlemin aynı mantıksal kaynakta aynı URL 'YI kullanması anlamına gelir. Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Razor Sayfalar uygulamalar, bir uygulamanın *Sayfalar* klasöründe adlandırılmış kaynaklara hizmeti sağlamak için varsayılan geleneksel yönlendirmeyi kullanır. Sayfa yönlendirme davranışını özelleştirmenizi sağlayan ek kurallar mevcuttur Razor . Daha fazla bilgi için <xref:razor-pages/index> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

URL oluşturma desteği, uygulamanın, uygulamayı birbirine bağlamak için sabit kodlama URL 'Leri olmadan geliştirilebilmesine izin verir. Bu destek, temel bir yönlendirme yapılandırmasıyla başlayıp uygulamanın kaynak düzeni belirlendikten sonra yolların değiştirilmesini sağlar.

Yönlendirme *endpoints* `Endpoint` , bir uygulamadaki mantıksal uç noktaları temsil etmek için uç noktaları () kullanır.

Bir uç nokta, istekleri işlemek için bir temsilci ve rastgele meta veri koleksiyonunu tanımlar. Meta veriler, her bir uç noktaya eklenen ilkelere ve yapılandırmaya bağlı olarak çapraz kesme sorunları uygulamak için kullanılır.

Yönlendirme sistemi aşağıdaki özelliklere sahiptir:

* Yol şablonu sözdizimi, simgeleştirilmiş yol parametrelerine sahip yolları tanımlamak için kullanılır.
* Geleneksel stil ve öznitelik stili uç nokta yapılandırmasına izin verilir.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> bir URL parametresinin belirli bir uç nokta kısıtlaması için geçerli bir değer içerip içermediğini belirlemekte kullanılır.
* MVC/sayfalar gibi uygulama modelleri, Razor yönlendirme senaryolarının öngörülebilir bir uygulaması olan tüm uç noktalarını kaydeder.
* Yönlendirme gerçekleştirme, yönlendirme kararlarını, ara yazılım ardışık düzeninde istediğiniz yere getirir.
* Bir yönlendirme ara yazılımı, belirli bir istek URI 'SI için yönlendirme ara yazılımı uç noktası kararının sonucunu inceleyebilir.
* Uygulamadaki tüm uç noktaları, ara yazılım ardışık düzeninde herhangi bir yerde listelemek mümkündür.
* Bir uygulama, uç nokta bilgilerine göre URL 'Ler oluşturmak için (örneğin, yeniden yönlendirme veya bağlantılar için) yönlendirmeyi kullanabilir ve bu sayede bakım yapılmasına yardımcı olan sabit kodlanmış URL 'Lerden kaçınabilirsiniz.
* URL oluşturma, rastgele genişletilebilirliği destekleyen adreslere dayalıdır:

  * Bağlantı Oluşturucu API 'SI ( <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ), URL oluşturmak için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) kullanılarak her yerde çözülebilir.
  * Bağlantı Oluşturucu API 'sinin dı aracılığıyla kullanılamadığı durumlarda <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> URL 'leri derlemek için yöntemler sunar.

> [!NOTE]
> ASP.NET Core 2,2 ' de uç nokta yönlendirme yayını ile, uç nokta bağlama MVC/ Razor sayfa eylemleri ve sayfaları ile sınırlıdır. Uç nokta bağlama yeteneklerinin genişletmeleri, gelecek sürümlerde planlanmaktadır.

Yönlendirme, sınıfı tarafından bulunan [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzenine bağlıdır <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> . [ASP.NET Core MVC](xref:mvc/overview) , yapılandırma kapsamında bir ara yazılım ardışık düzenine yönlendirme ekler ve MVC ve sayfalar uygulamalarında yönlendirmeyi işler Razor . Tek başına bileşen olarak yönlendirmeyi nasıl kullanacağınızı öğrenmek için [yönlendirme ara yazılımı kullanma](#use-routing-middleware) bölümüne bakın.

### <a name="url-matching"></a>URL eşleştirme

URL eşleştirme, yönlendirmenin bir *uç noktaya*gelen isteği gönderdiği işlemdir. Bu işlem, URL yolundaki verileri temel alır, ancak istekteki verileri göz önünde bulundurmanız için genişletilebilir. Ayrı işleyicilere istek gönderme özelliği, bir uygulamanın boyutunu ve karmaşıklığını ölçeklendirmeye yönelik anahtardır.

Uç nokta yönlendirmesinde yönlendirme sistemi tüm gönderme kararlarından sorumludur. Ara yazılım, seçili uç noktaya göre ilkeler uyguladığı için, yönlendirme sisteminde güvenlik ilkelerinin dağıtımını etkileyebilecek veya uygulamanın uygulanmasını etkileyebilecek herhangi bir kararın olması önemlidir.

Uç nokta temsilcisi yürütüldüğünde, [Routecontext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özellikleri, bu nedenle yapılan istek işlemeye bağlı olarak uygun değerlere ayarlanır.

[RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) , rotada oluşturulan *yol değerlerinin* bir sözlüğüdür. Bu değerler genellikle URL 'YI simgeleştirerek belirlenir ve Kullanıcı girişini kabul etmek ya da uygulama içinde daha fazla kararlar almak için kullanılabilir.

[RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) , eşleşen rotayla ilgili ek verilerin bir özellik çantadır. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> Her rotayla durum verilerinin ilişkilendirilmesini desteklemek için sağlanır, böylece uygulama hangi yolun eşleştiğini temel alarak kararlar alabilir. Bu değerler, geliştirici tarafından tanımlanır ve yönlendirme davranışını herhangi bir **şekilde etkilemez.** Ayrıca, [RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) içinde bulunan değerler her türlü türden olabilir. Bu, [veri](xref:Microsoft.AspNetCore.Routing.RouteData.Values)dizeleri arasında dönüştürülebilir olmalıdır.

[RouteData. yönlendiriciler](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) , isteği başarıyla eşleştirirken geçen yolların bir listesidir. Yollar bir diğerinin içinde iç içe olabilir. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>Özelliği, bir eşleşme ile sonuçlanan yolların mantıksal ağacı aracılığıyla yolu yansıtır. Genellikle, içindeki ilk öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> yol koleksiyonudur ve URL oluşturma için kullanılmalıdır. İçindeki son öğe, <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşen yol işleyicisidir.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>LinkGenerator ile URL oluşturma

URL oluşturma, yönlendirmenin bir yol değerleri kümesine göre bir URL yolu oluşturabileceği işlemdir. Bu, uç noktalarınız ve bunlara erişen URL 'Ler arasında mantıksal bir ayrım sağlar.

Endpoint Routing, bağlantı Oluşturucu API 'SI ( <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ) içerir. <xref:Microsoft.AspNetCore.Routing.LinkGenerator> , [dı](xref:fundamentals/dependency-injection)'den alınabilecek bir tek hizmettir. API, yürütülen bir istek bağlamı dışında kullanılabilir. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro), HTML Yardımcıları ve [eylem sonuçları](xref:mvc/controllers/actions)gibi mvc 'nin ve senaryolarına yönelik senaryolar, bağlantı oluşturma özellikleri sağlamak için bağlantı oluşturucuyu kullanır.

Bağlantı Oluşturucu, bir *Adres* ve *Adres şemaları*kavramıyla desteklenir. Adres şeması, bağlantı oluşturma için göz önünde bulundurmanız gereken uç noktaları belirlemenin bir yoludur. Örneğin, çok sayıda kullanıcının yol adı ve yol değerleri senaryoları, MVC/ Razor sayfalardan bir adres düzeni olarak uygulanır.

Bağlantı Oluşturucu, MVC/ Razor Pages eylemlerine ve sayfalarına aşağıdaki genişletme yöntemleri aracılığıyla bağlanabilir:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Bu yöntemlerin aşırı yüklemesi, içeren bağımsız değişkenleri kabul eder `HttpContext` . Bu yöntemler işlevsel olarak eşdeğerdir, `Url.Action` `Url.Page` ancak ek esneklik ve seçenekler sunar.

`GetPath*`Yöntemler, `Url.Action` `Url.Page` mutlak bir yol içeren bir URI oluşturabilen ve ' a benzerdir. `GetUri*`Yöntemler her zaman bir düzen ve konak içeren mutlak BIR URI oluşturur. Bir öğesini kabul eden yöntemler, `HttpContext` yürütülmekte olan istek bağlamında BIR URI oluşturur. Ortam yolu değerleri, URL taban yolu, şeması ve yürütülen istekten ana bilgisayar, geçersiz kılınmadıkça kullanılır.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> bir adresle çağırılır. URI oluşturma iki adımda gerçekleşir:

1. Adres, adresle eşleşen bir uç nokta listesine bağlanır.
1. Her uç nokta `RoutePattern` , sağlanan değerlerle eşleşen bir yol deseninin bulunana kadar değerlendirilir. Elde edilen çıktı, bağlantı oluşturucuya sağlanan diğer URI parçalarıyla birleştirilir ve döndürülür.

Tarafından sunulan yöntemler, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> herhangi bir adres türü için standart bağlantı oluşturma yeteneklerini destekler. Bağlantı oluşturucuyu kullanmanın en kolay yolu, belirli bir adres türü için işlem gerçekleştiren genişletme yöntemlerine yöneliktir.

| Genişletme yöntemi   | Açıklama                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Belirtilen değerleri temel alarak mutlak bir yola sahip bir URI oluşturur. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Belirtilen değerleri temel alarak mutlak bir URI oluşturur.             |

> [!WARNING]
> Çağırma yöntemlerinin aşağıdaki etkilerine dikkat edin <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * `GetUri*`Gelen isteklerin üstbilgisini doğrulayan bir uygulama yapılandırmasında uzantı yöntemlerini dikkatle kullanın `Host` . `Host`Gelen isteklerin üstbilgisi doğrulandıktan sonra, güvenilir olmayan istek girişi, bir görünüm/sayfada URI 'ler halinde istemciye geri gönderilebilir. Tüm üretim uygulamalarının, `Host` üst bilgisini bilinen geçerli değerlere karşı doğrulamak için kendi sunucusunu yapılandırmasını öneririz.
>
> * <xref:Microsoft.AspNetCore.Routing.LinkGenerator>Veya ile birlikte ara yazılım içinde dikkatli kullanın `Map` `MapWhen` . `Map*` yürütülen isteğin temel yolunu değiştirir ve bu da bağlantı oluşturma çıktısını etkiler. Tüm <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 'ler temel yol belirtilmesine izin verir. Bağlantı oluşturma işlemi geri almak için her zaman boş bir temel yol belirtin `Map*` .

## <a name="differences-from-earlier-versions-of-routing"></a>Yönlendirmenin önceki sürümlerinden farklılıklar

ASP.NET Core 2,2 veya üzeri ve daha önceki yönlendirme sürümlerindeki ASP.NET Core uç nokta yönlendirmesi arasında birkaç fark vardır:

* Uç nokta yönlendirme sistemi <xref:Microsoft.AspNetCore.Routing.IRouter> , devralma dahil olmak üzere tabanlı genişletilebilirliği desteklemez <xref:Microsoft.AspNetCore.Routing.Route> .

* Uç nokta yönlendirme [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)desteklemez. Uyumluluk Shim 'yi kullanmaya devam etmek için 2,1 [Uyumluluk sürümünü](xref:mvc/compatibility-version) ( `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)` ) kullanın.

* Uç nokta yönlendirmesinde, geleneksel yollar kullanılırken oluşturulan URI 'lerin büyük küçük harfleri için farklı davranış vardır.

  Aşağıdaki varsayılan yol şablonunu göz önünde bulundurun:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Aşağıdaki rotayı kullanarak bir eyleme bağlantı oluşturduğunuzu varsayalım:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <xref:Microsoft.AspNetCore.Routing.IRouter>Tabanlı yönlendirme ile, bu kod, `/blog/ReadPost/17` belirtilen rota değerinin büyük küçük harflerini BELIRTEN bir URI oluşturur. ASP.NET Core 2,2 veya üzeri bir nokta yönlendirme `/Blog/ReadPost/17` ("blog" büyük harfli) üretir. Endpoint Routing, `IOutboundParameterTransformer` Bu davranışı genel olarak özelleştirmek veya URL eşleme için farklı kurallar uygulamak üzere kullanılabilecek arabirimi sağlar.

  Daha fazla bilgi için bkz. [Parameter transformatör başvurusu](#parameter-transformer-reference) bölümü.

* Geleneksel yolların bulunduğu MVC/sayfalar tarafından kullanılan bağlantı üretimi, Razor mevcut olmayan bir denetleyiciye/eyleme veya sayfaya bağlantı kurmaya çalışırken farklı şekilde davranır.

  Aşağıdaki varsayılan yol şablonunu göz önünde bulundurun:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Aşağıdaki ile varsayılan şablonu kullanarak bir eyleme bağlantı oluşturduğunuzu varsayalım:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  `IRouter`Tabanlı yönlendirme ile, `/Blog/ReadPost/17` `BlogController` mevcut olmasa veya bir eylem yöntemine sahip olmasalar bile sonuç her zaman olur `ReadPost` . Beklendiği gibi, ASP.NET Core 2,2 veya sonraki bir sürümde uç nokta yönlendirme `/Blog/ReadPost/17` eylem yöntemi varsa üretir. *Ancak, uç nokta yönlendirme, eylem yoksa boş bir dize oluşturur.* Kavramsal olarak, uç nokta yönlendirme, eylem mevcut değilse uç noktanın var olduğunu varsaymaz.

* Bağlantı oluşturma *çevresel değeri ınvalidation algoritması* , uç nokta yönlendirme ile kullanıldığında farklı davranır.

  *Çevresel değer ınvalidation* , şu anda yürütülmekte olan isteğin (çevresel değerler) hangi rota değerlerinin bağlantı oluşturma işlemlerinde kullanılabileceğini belirleyen algoritmadır. Geleneksel yönlendirme, farklı bir eyleme bağlanılırken ek yol değerlerini her zaman geçersiz kıldı. ASP.NET Core 2,2 sürümünden önce öznitelik yönlendirmesinde bu davranış yoktu. ASP.NET Core 'nin önceki sürümlerinde, aynı yol parametresi adlarını kullanan başka bir eyleme bağlantılar, bağlantı oluşturma hatalarıyla sonuçlandı. ASP.NET Core 2,2 veya sonraki sürümlerde, yönlendirme biçimlerinin her ikisi de başka bir eyleme bağlanırken değerleri geçersiz kılar.

  ASP.NET Core 2,1 veya önceki sürümlerde aşağıdaki örneği göz önünde bulundurun. Başka bir eyleme (veya başka bir sayfaya) bağlanırken, yol değerleri istenmeyen yollarla yeniden kullanılabilir.

  */Pages/Store/Product.exe*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  */Pages/Login.exe*içinde:

  ```cshtml
  @page "{id?}"
  ```

  URI `/Store/Product/18` ASP.NET Core 2,1 veya daha önceki bir sürümdeyse, tarafından Store/Info sayfasında oluşturulan bağlantı `@Url.Page("/Login")` olur `/Login/18` . `id`16 değeri, bağlantı hedefi uygulamanın tamamen farklı bir parçası olsa da yeniden kullanılır. `id`Sayfa bağlamındaki yol değeri `/Login` büyük olasılıkla bir mağaza ürün kimliği değeri değil, BIR kullanıcı kimliği değeridir.

  ASP.NET Core 2,2 veya sonraki bir sürümü ile Endpoint Routing de sonuç olur `/Login` . Bağlantılı hedef farklı bir eylem veya sayfa olduğunda çevresel değerler yeniden kullanılmaz.

* Gidiş dönüşü yol parametresi sözdizimi: çift yıldız ( `**` ) catch-all parametre sözdizimi kullanılırken eğik çizgiler kodlanmaz.

  Bağlantı oluşturma sırasında, yönlendirme sistemi, eğik çizgiler hariç, bir çift yıldız ( `**` ) catch-all parametresinde (örneğin,) yakalanan değeri kodlar `{**myparametername}` . Çift yıldız yakalama, `IRouter` ASP.NET Core 2,2 veya üzeri sürümlerde tabanlı yönlendirme ile desteklenir.

  ASP.NET Core () öğesinin önceki sürümlerindeki tek yıldız catch-all parametre sözdizimi `{*myparametername}` desteklenmeye devam eder ve eğik çizgi kodlandı.

  | Yol              | İle oluşturulan bağlantı<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (eğik çizgi kodlandı)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Ara yazılım örneği

Aşağıdaki örnekte, bir ara yazılım, <xref:Microsoft.AspNetCore.Routing.LinkGenerator> Mağaza ürünlerini listeleyen bir eylem yöntemine bağlantı oluşturmak için API 'yi kullanır. Bağlantı Oluşturucuyu bir sınıfa ekleme ve çağırarak bir `GenerateLink` uygulamadaki herhangi bir sınıf için kullanılabilir.

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

Çoğu uygulama, <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ' de tanımlanan benzer uzantı yöntemlerinden birini çağırarak veya arayarak yollar oluşturur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> . <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>Uzantı yöntemlerinden herhangi biri bir örneği oluşturur <xref:Microsoft.AspNetCore.Routing.Route> ve bunu yol koleksiyonuna ekler.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> yol işleyici parametresini kabul etmez. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> yalnızca tarafından işlenen rotaları ekler <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> . MVC 'de yönlendirme hakkında daha fazla bilgi için bkz <xref:mvc/controllers/routing> ..

Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> tipik bir ASP.NET Core MVC yol tanımı tarafından kullanılan bir çağrının örneğidir:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Bu şablon bir URL yoluyla eşleşir ve yol değerlerini ayıklar. Örneğin, yol `/Products/Details/17` Şu yol değerlerini oluşturur: `{ controller = Products, action = Details, id = 17 }` .

Rota değerleri, URL yolunun kesimlere bölünüyor ve rota şablonundaki *yol parametresi* adı ile her bir segmenti eşleştirerek belirlenir. Yol parametreleri olarak adlandırılır. Küme ayraçları içinde parametre adının çevreleme tarafından tanımlanan parametreler `{ ... }` .

Yukarıdaki şablon URL yoluyla da eşleştirebilir `/` ve değerleri üretebilir `{ controller = Home, action = Index }` . Bu durum, `{controller}` ve `{action}` Rota parametrelerinin varsayılan değerleri olduğu ve `id` route parametresinin isteğe bağlı olması nedeniyle oluşur. Bir eşittir işareti ( `=` ), yol parametre adından sonra gelen bir değer, parametre için varsayılan bir değer tanımlar. `?`Yol parametre adından sonra bir soru işareti () isteğe bağlı bir parametre tanımlar.

Yol parametreleri varsayılan değer ile *her zaman* rota değeri oluşturur. İsteğe bağlı parametreler, karşılık gelen bir URL yol kesimi yoksa rota değeri oluşturmaz. Yol şablonu senaryolarının ve sözdiziminin kapsamlı bir açıklaması için [yol şablonu başvurusu](#route-template-reference) bölümüne bakın.

Aşağıdaki örnekte, Route parametresi tanımı `{id:int}` yol parametresi için bir [yol kısıtlaması](#route-constraint-reference) tanımlar `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Bu şablon, gibi bir URL yoluyla eşleşir `/Products/Details/17` `/Products/Details/Apples` . Yol kısıtlamaları <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , yol değerlerini uygulayıp onları doğrulamak için inceler. Bu örnekte, yol değeri `id` bir tamsayıya dönüştürülebilir olmalıdır. Framework tarafından sunulan yol kısıtlamalarının açıklaması için bkz. [route-Constraint-Reference](#route-constraint-reference) .

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>, Ve için değer kabul etmenin ek aşırı yüklemeleri `constraints` `dataTokens` `defaults` . Bu parametrelerin tipik kullanımı, anonim türdeki özellik adlarının yol parametre adlarıyla eşleşen anonim olarak yazılmış bir nesneyi geçirmektir.

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
> Kısıtlamaları ve Varsayılanları tanımlamaya yönelik satır içi sözdizimi basit yollar için kullanışlı olabilir. Ancak, satır içi sözdizimi tarafından desteklenmeyen veri belirteçleri gibi senaryolar vardır.

Aşağıdaki örnek birkaç ek senaryoyu göstermektedir:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Önceki şablon, benzer bir URL yoluyla eşleşir `/Blog/All-About-Routing/Introduction` ve değerleri ayıklar `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` . Ve için varsayılan yol değerleri `controller` , `action` şablonda karşılık gelen hiçbir yol parametresi olmasa bile rota tarafından üretilir. Varsayılan değerler yol şablonunda belirtilebilir. Route `article` parametresi, yol parametre adından önce bir çift yıldız işareti () görünümüne göre *catch-all* olarak tanımlanır `**` . Catch-all yol parametreleri, URL yolunun kalanını yakalar ve boş dizeyle de aynı olabilir.

Aşağıdaki örnek yol kısıtlamalarını ve veri belirteçlerini ekler:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Önceki şablon, benzer bir URL yoluyla eşleşir `/en-US/Products/5` ve değerleri `{ controller = Products, action = Details, id = 5 }` ve veri belirteçlerini ayıklar `{ locale = en-US }` .

![Yereller Windows belirteçleri](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Yol sınıfı URL 'SI oluşturma

Sınıf, yol <xref:Microsoft.AspNetCore.Routing.Route> değerlerini bir kümesini rota şablonuyla BIRLEŞTIREREK URL oluşturma işlemi de gerçekleştirebilir. Bu, URL yolunu eşleştirmenin mantıksal bir işlemdir.

> [!TIP]
> URL oluşturmayı daha iyi anlamak için, oluşturmak istediğiniz URL 'YI düşünün ve sonra bir yönlendirme şablonunun bu URL ile nasıl eşleşeceğini düşünün. Hangi değerler üretilemidir? Bu, URL oluşturma işlevinin sınıfında nasıl çalıştığına ilişkin kaba bir eştir <xref:Microsoft.AspNetCore.Routing.Route> .

Aşağıdaki örnek genel ASP.NET Core MVC varsayılan yolunu kullanır:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Yol değerleriyle `{ controller = Products, action = List }` , URL `/Products/List` oluşturulur. Yol değerleri, URL yolunu biçimlendirmek için karşılık gelen yol parametrelerinin yerine kullanılır. `id`, İsteğe bağlı bir yol parametresi olduğundan, URL 'si için bir değer olmadan başarıyla oluşturulur `id` .

Yol değerleriyle `{ controller = Home, action = Index }` , URL `/` oluşturulur. Belirtilen yol değerleri varsayılan değerlerle eşleşir ve varsayılan değerlere karşılık gelen segmentler güvenle atlanır.

Her iki URL de aşağıdaki yol tanımıyla (ve) birlikte gidiş dönüş, `/Home/Index` `/` URL oluşturmak için kullanılan rota değerlerini oluşturur.

> [!NOTE]
> ASP.NET Core MVC kullanan bir uygulama <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> , doğrudan yönlendirmeye çağırmak yerine URL 'ler oluşturmak için kullanılmalıdır.

URL oluşturma hakkında daha fazla bilgi için [URL oluşturma başvurusu](#url-generation-reference) bölümüne bakın.

## <a name="use-routing-middleware"></a>Yönlendirme ara yazılımı kullanma

Uygulamanın proje dosyasındaki [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurun.

İçindeki hizmet kapsayıcısına yönlendirme Ekle `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Yolların yönteminde yapılandırılması gerekir `Startup.Configure` . Örnek uygulama aşağıdaki API 'Leri kullanır:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Yalnızca HTTP GET isteklerini eşleştirir.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Aşağıdaki tabloda verilen URI 'Ler ile ilgili yanıtlar gösterilmektedir.

| URI                    | Yanıt                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Herkese! Rota değerleri: [işlem, oluşturma], [kimlik, 3] |
| `/package/track/-3`    | Herkese! Rota değerleri: [işlem, izleme], [kimlik,-3] |
| `/package/track/-3/`   | Herkese! Rota değerleri: [işlem, izleme], [kimlik,-3] |
| `/package/track/`      | İstek, eşleşme olmadan üzerinden yapılır.              |
| `GET /hello/Joe`       | Merhaba, ali!                                          |
| `POST /hello/Joe`      | İstek üzerinden geçer, yalnızca HTTP GET ile eşleşir. |
| `GET /hello/Joe/Smith` | İstek, eşleşme olmadan üzerinden yapılır.              |

Framework, yollar () oluşturmak için bir genişletme yöntemleri kümesi sağlar <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions> :

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

`Map[Verb]`Yöntemler, yöntem ADıNDAKI http fiili ile rotayı sınırlandırmak için kısıtlamalar kullanır. Örnek için bkz. <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> ve <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Rota şablonu başvurusu

Küme ayraçları () içindeki belirteçler `{ ... }` , yol eşleştirildiği takdirde bağlanan *Rota parametrelerini* tanımlar. Bir yol segmentinde birden fazla yol parametresi tanımlayabilirsiniz, ancak bunların bir değişmez değer ile ayrılması gerekir. Örneğin, `{controller=Home}{action=Index}` ve arasında değişmez değer olmadığından geçerli bir yol değil `{controller}` `{action}` . Bu rota parametrelerinin bir adı olmalı ve ek öznitelikler belirtilmiş olabilir.

Yol parametrelerinden (örneğin, `{id}` ) ve yol ayırıcısından farklı bir metin, `/` URL içindeki metinle eşleşmelidir. Metin eşleştirme, büyük/küçük harfe duyarsızdır ve URL yolunun kodu çözülmüş gösterimine göre yapılır. Bir sabit yol parametresi sınırlayıcısından ( `{` veya) eşleştirmek için `}` , karakteri (veya) tekrarlayarak sınırlayıcıdan kaçış `{{` `}}` .

İsteğe bağlı bir dosya uzantısına sahip bir dosya adı yakalamaya deneyen URL desenlerinin ek konuları vardır. Örneğin, şablonu göz önünde bulundurun `files/{filename}.{ext?}` . Hem hem de için değerler olduğunda `filename` `ext` her iki değer de doldurulur. URL 'de yalnızca bir değeri `filename` varsa, sondaki nokta ( `.` ) isteğe bağlı olduğundan yol eşleşir. Aşağıdaki URL 'Ler bu rota ile eşleşiyor:

* `/files/myFile.txt`
* `/files/myFile`

`*` `**` URI 'nin geri kalanına bağlamak için bir yol parametresinin öneki olarak bir yıldız işareti () veya çift yıldız işareti () kullanabilirsiniz. Bunlara *catch-all* parametreleri denir. Örneğin, `blog/{**slug}` ile başlayan `/blog` ve bunu izleyen bir değere sahip olan ve yol değerine atanan HERHANGI bir URI ile eşleşir `slug` . Catch-all parametreleri boş dizeyle de aynı olabilir.

Yol ayırıcısı () karakterleri de dahil olmak üzere bir URL oluşturmak için, catch-all parametresi uygun karakterleri de çıkar `/` . Örneğin, `foo/{*path}` yol değerlerini içeren yol `{ path = "my/path" }` oluşturulur `foo/my%2Fpath` . Atlanan eğik çizgiye göz önünde edin. Yol ayırıcı karakterlerini yuvarlaklaştırmak için `**` Rota parametresi önekini kullanın. İle rota `foo/{**path}` `{ path = "my/path" }` oluşturulur `foo/my/path` .

Yol parametreleri, parametre adından sonra bir eşittir işaretiyle () ayırarak varsayılan değer belirtilerek belirlenmiş *varsayılan değerlere* sahip olabilir `=` . Örneğin, `{controller=Home}` `Home` için varsayılan değer olarak tanımlar `controller` . Parametresi için URL 'de hiçbir değer yoksa varsayılan değer kullanılır. Yol parametreleri `?` , içinde olduğu gibi parametre adının sonuna bir soru işareti () eklenerek isteğe bağlı olarak yapılır `id?` . İsteğe bağlı değerler ve varsayılan yol parametreleri arasındaki fark, varsayılan değere sahip bir yol parametresinin her zaman bir değer ürettiğinden, &mdash; isteğe bağlı bir parametre yalnızca Istek URL 'si tarafından bir değer sağlandığı zaman bir değere sahip olur.

Rota parametrelerinin URL 'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamaları olabilir. `:`Yol parametre adından sonra bir iki nokta () ve kısıtlama adı eklemek, bir rota parametresinde bir *satır içi kısıtlamayı* belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, `(...)` kısıtlama adından sonra parantez () içine alınır. Birden çok satır içi kısıtlama, başka bir iki nokta ( `:` ) ve kısıtlama adı eklenerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL işlemede kullanılmak üzere bir örneği oluşturmak için hizmetine geçirilir. Örneğin, yol şablonu `blog/{article:minlength(10)}` `minlength` bağımsız değişkenle bir kısıtlama belirtir `10` . Yol kısıtlamaları ve Framework tarafından sunulan kısıtlamaların bir listesi hakkında daha fazla bilgi için, [route kısıtlama başvurusu](#route-constraint-reference) bölümüne bakın.

Yol parametrelerinde Ayrıca, bağlantı oluştururken ve URL 'Ler ile eşleşen eylemler ve sayfalar için bir parametrenin değerini dönüştüren parametre dönüştürücüler bulunabilir. LIKE kısıtlamaları, yol parametre adından sonra iki nokta üst üste ( `:` ) ve transformatör adı eklenerek, parametre dönüştürücüleri bir rota parametresine eklenebilir. Örneğin, yol şablonu `blog/{article:slugify}` bir `slugify` transformatör belirtir. Parametre dönüştürücüler hakkında daha fazla bilgi için bkz. [Parameter transformatör başvurusu](#parameter-transformer-reference) bölümü.

Aşağıdaki tabloda örnek yol şablonları ve bunların davranışları gösterilmektedir.

| Rota şablonu                           | Örnek eşleşen URI    | İstek URI 'SI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Yalnızca tek bir yolla eşleşir `/hello` .                                     |
| `{Page=Home}`                            | `/`                     | İle eşleşir ve `Page` ayarlanır `Home` .                                         |
| `{Page=Home}`                            | `/Contact`              | İle eşleşir ve `Page` ayarlanır `Contact` .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products`Denetleyiciye ve `List` eyleme eşlenir.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products`Denetleyici ve `Details` eyleme eşlenir ( `id` 123 olarak ayarlanır). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home`Denetleyiciye ve `Index` yönteme eşlenir ( `id` yok sayılır).        |

Bir şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar, yol şablonu dışında da belirtilebilir.

> [!TIP]
> İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının nasıl yapıldığını görmek için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin <xref:Microsoft.AspNetCore.Routing.Route> .

## <a name="reserved-routing-names"></a>Ayrılmış yönlendirme adları

Aşağıdaki anahtar sözcükler ayrılmış isimlerdir ve yol adları veya parametreler olarak kullanılamaz:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Yol kısıtlama başvurusu

Yol kısıtlamaları, gelen URL 'de bir eşleşme meydana geldiğinde ve URL yolu yol değerlerinde simgeleştirilir yürütülür. Rota kısıtlamaları genellikle yol şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda bir Evet/Hayır kararı getirir. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilmeyeceğini göz önünde bulundurmanız için yol değeri dışındaki verileri kullanır. Örneğin, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> bir ISTEğI http fiiline bağlı olarak kabul edebilir veya reddedebilir. Kısıtlamalar, yönlendirme isteklerinde ve bağlantı oluşturmada kullanılır.

> [!WARNING]
> **Giriş doğrulaması**için kısıtlamaları kullanmayın. **Giriş doğrulaması**için kısıtlamalar kullanılıyorsa, doğru bir hata iletisine sahip *400-Bad isteği* yerine *404-* olmayan bir Yanıt ile geçersiz giriş oluşur. Yol kısıtlamaları, belirli bir rota için girdileri doğrulamak üzere değil, benzer yolların **belirsizliğini ortadan** kaldırmak için kullanılır.

Aşağıdaki tabloda örnek yol kısıtlamaları ve bunların beklenen davranışları gösterilmektedir.

| Kısıtlaması | Örnek | Örnek eşleşmeler | Notlar |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir tamsayıyla eşleşir.|
| `bool` | `{active:bool}` | `true`, `FALSE` | Veya ile eşleşir `true` `false` . Büyük/küçük harf duyarsız.|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | `DateTime`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | `decimal`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | `double`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | `float`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Geçerli bir `Guid` değerle eşleşir.|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli bir `long` değerle eşleşir.|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Dize en az 4 karakter olmalıdır.|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Dizede en fazla 8 karakter vardır.|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Dize tam olarak 12 karakter uzunluğunda olmalıdır.|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Dize en az 8 olmalı ve en fazla 16 karakter uzunluğunda olmalıdır.|
| `min(value)` | `{age:min(18)}` | `19` | Tamsayı değeri en az 18 olmalıdır.|
| `max(value)` | `{age:max(120)}` | `91` | Tamsayı değeri üst sınırı 120.|
| `range(min,max)` | `{age:range(18,120)}` | `91` | Tamsayı değeri en az 18 ve en fazla 120 olmalıdır.|
| `alpha` | `{name:alpha}` | `Rick` | Dize bir veya daha fazla alfabetik karakterden oluşmalıdır `a` - `z` . Büyük/küçük harf duyarsız.|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Dize, normal ifadeyle eşleşmelidir. Normal ifade tanımlama hakkında ipuçlarına bakın.|
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre olmayan bir değerin mevcut olduğunu zorlamak için kullanılır.|

Birden çok, iki nokta üst üste sınırlı kısıtlama tek bir parametreye uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle kısıtlar:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` `DateTime` , her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar. Framework tarafından sunulan yol kısıtlamaları, yol değerlerinde depolanan değerleri değiştirmez. URL 'den Ayrıştırılan tüm rota değerleri dizeler olarak depolanır. Örneğin, `float` kısıtlama yol değerini bir float öğesine dönüştürmeye çalışır, ancak dönüştürülen değer yalnızca bir float öğesine dönüştürülebileceğini doğrulamak için kullanılır.

## <a name="regular-expressions"></a>Normal ifadeler

ASP.NET Core Framework, `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` normal ifade oluşturucusuna ekler. <xref:System.Text.RegularExpressions.RegexOptions>Bu üyelerin açıklaması için bkz..

Normal ifadeler, Yönlendirme ve C# dili tarafından kullanılanlarla benzerlik olan sınırlayıcıları ve belirteçleri kullanır. Normal ifade belirteçlerinin atlanmalıdır. Yönlendirmenin normal ifadesini kullanmak için `^\d{3}-\d{2}-\d{4}$` :

* İfade, `\` dize içinde, kaynak kodunda çift ters eğik çizgi olarak belirtilen tek ters eğik çizgi karakterleri içermelidir `\\` .
* Normal ifade, `\\` dize kaçış karakterinden kaçınmak için bizim için olmalıdır `\` .
* Normal ifade, tam `\\` [dize sabit değerleri](/dotnet/csharp/language-reference/keywords/string)kullanılırken gerekli değildir.

Yönlendirme parametresi sınırlayıcı karakterleri,,,, `{` `}` `[` `]` ifadesindeki karakterleri çift `{{` `}` `[[` `]]` kaçış,,,,,,,,,,,. Aşağıdaki tabloda, bir normal ifade ve kaçan sürümü gösterilmektedir:

| Normal ifade    | Kaçan normal Ifade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmelerde kullanılan normal ifadeler, genellikle giriş işareti `^` karakteriyle başlar ve dizenin başlangıç konumuyla eşleşir. İfadeler, genellikle dolar işareti `$` karakteriyle biter ve dizenin sonuyla eşleşir. `^`Ve `$` karakterleri, normal ifadenin tüm yol parametresi değeri ile eşleştiğinden emin olun. `^`Ve karakterleri olmadan `$` normal ifade, dize içindeki herhangi bir alt dizeden eşleşir ve bu genellikle istenmeyen bir ifadedir. Aşağıdaki tabloda örnekler verilmektedir ve bunların eşleşmesinin neden eşleşmediği veya eşleşmemesi açıklanmaktadır.

| Expression   | Dize    | Eşleştirme | Yorum               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | MZ        | Yes   | Eşleşen ifadesi    |
| `[a-z]{2}`   | MZ        | Yes   | Büyük/küçük harfe duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | Bkz. `^` ve `$` üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | Bkz. `^` ve `$` üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için bkz. [.NET Framework normal ifadeler](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için, normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` yalnızca `action` Rota değeri `list` ,, veya ile eşleşir `get` `create` . Kısıtlama sözlüğüne geçirilirse dize `^(list|get|create)$` eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyen kısıtlama sözlüğüne (bir şablon içinde satır içi değil) geçirilen kısıtlamalar da normal ifadeler olarak kabul edilir.

## <a name="custom-route-constraints"></a>Özel yol kısıtlamaları

Yerleşik yol kısıtlamalarına ek olarak, arabirimi uygulayarak özel yol kısıtlamaları oluşturulabilir <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> . <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>Arabirim, `Match` `true` kısıtlama karşılanıp Aksi takdirde döndüren tek bir yöntemi içerir `false` .

Özel bir kullanmak için <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> yol kısıtlama türü, uygulamanın uygulamanın hizmet kapsayıcısında kayıtlı olması gerekir <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> . <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>, Yol kısıtlama anahtarlarını <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> Bu kısıtlamaları doğrulayan uygulamalarla eşleyen bir sözlüktür. Bir uygulama <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> , `Startup.ConfigureServices` hizmetlerin bir parçası olarak ' de güncelleştirilir [. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) çağrısı veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan ile yapılandırma `services.Configure<RouteOptions>` . Örneğin:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Kısıtlama daha sonra, kısıtlama türü kaydedilirken belirtilen ad kullanılarak yollara her zamanki şekilde uygulanabilir. Örneğin:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Parametre transformatörü başvurusu

Parametre dönüştürücüler:

* Bir bağlantısını oluştururken yürütün <xref:Microsoft.AspNetCore.Routing.Route> .
* Uygulayın `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer` .
* Kullanılarak yapılandırılır <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> .
* Parametrenin yol değerini alın ve yeni bir dize değerine dönüştürün.
* Oluşturulan bağlantıda Dönüştürülmüş değerin kullanılmasına neden olacak.

Örneğin, yol düzeninde bir özel `slugify` parametre transformatörü `blog\{article:slugify}` `Url.Action(new { article = "MyTestArticle" })` oluşturur `blog\my-test-article` .

Bir parametre transformatörü bir yol düzeninde kullanmak için, önce ' de kullanarak bunu <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> yapılandırın `Startup.ConfigureServices` :

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametre dönüştürücüler, bir uç noktanın çözümlediği URI 'yi dönüştürmek için çerçevesi tarafından kullanılır. Örneğin, ASP.NET Core MVC,,, ve ile eşleşecek şekilde kullanılan rota değerini dönüştürmek için parametre dönüştürücüler kullanır `area` `controller` `action` `page` .

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Önceki yol ile, eylem `SubscriptionManagementController.GetAll` URI ile eşleştirilir `/subscription-management/get-all` . Bir parametre transformatörü bir bağlantı oluşturmak için kullanılan rota değerlerini değiştirmez. Örneğin, `Url.Action("GetAll", "SubscriptionManagement")` çıktılar `/subscription-management/get-all` .

ASP.NET Core, oluşturulan yollarla bir parametre dönüştürücüler kullanmak için API kuralları sağlar:

* ASP.NET Core MVC 'nin `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API kuralı vardır. Bu kural, uygulamadaki tüm öznitelik yollarına belirtilen bir parametre transformatörü uygular. Parametre transformatörü, öznitelik yol belirteçlerini değiştirildiklerinde dönüştürür. Daha fazla bilgi için bkz. [belirteç değişimini özelleştirmek için bir parametre transformatörü kullanma](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Sayfaların `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API kuralı vardır. Bu kural, belirtilen bir parametre transformatörü otomatik olarak bulunan tüm Razor sayfalara uygular. Parametre transformatörü, sayfa yollarının klasör ve dosya adı segmentlerini dönüştürür Razor . Daha fazla bilgi için bkz. [sayfa yollarını özelleştirmek için bir parametre transformatörü kullanma](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Aşağıdaki örnek, yol değerlerinin ve bir sözlüğünün bir sözlüğüne verilen bir yolun bağlantısının nasıl oluşturulacağını gösterir <xref:Microsoft.AspNetCore.Routing.RouteCollection> .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>Önceki örnek sonunda oluşturulan `/package/create/123` . Sözlük, `operation` `id` "paket yolunu izle" şablonunun ve rota değerlerini sağlar `package/{operation}/{id}` . Ayrıntılar için, [yönlendirme ara yazılımı kullanma](#use-routing-middleware) bölümünde veya [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)örnek koda bakın.

Oluşturucunun ikinci parametresi <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> bir *ortam değerleri*koleksiyonudur. Ortam değerleri, bir geliştiricinin bir istek bağlamı içinde belirtmesi gereken değer sayısını sınırlandırdığından kullanım için uygundur. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. ASP.NET Core MVC uygulamasının `About` öğesinin eyleminde, `HomeController` `Index` &mdash; ortam değeri kullanılan eyleme bağlamak için denetleyici yol değerini belirtmeniz gerekmez `Home` .

Bir parametreyle eşleşmeyen çevresel değerler yok sayılır. Ayrıca, açıkça sağlanmış bir değer çevresel değeri geçersiz kıldığında çevresel değerler de yoksayılır. Eşleştirme, URL 'de soldan sağa doğru gerçekleşir.

Açık olarak sağlanmış ancak yolun bir segmentiyle eşleşmeyen değerler sorgu dizesine eklenir. Aşağıdaki tabloda, yol şablonu kullanılırken sonuç gösterilmektedir `{controller}/{action}/{id?}` .

| Çevresel değerler                     | Açık değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "giriş"                | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | denetleyici = "Order", Action = "hakkında" | `/Order/About`          |
| denetleyici = "giriş", renk = "kırmızı" | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | Action = "hakkında", color = "Red"        | `/Home/About?color=Red` |

Bir rotada bir parametreye karşılık gelen bir varsayılan değer varsa ve bu değer açıkça sağlanmışsa, varsayılan değerle eşleşmelidir:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Bağlantı oluşturma yalnızca ve için eşleşen değerler sağlandığında bu yol için bir bağlantı oluşturur `controller` `action` .

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin `[Route("/x{token}y")]` ), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir. Karmaşık segmentlerin nasıl eşleştirileceği hakkında ayrıntılı bir açıklama için [bu koda](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) bakın. [Kod örneği](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) ASP.NET Core tarafından kullanılmaz, ancak karmaşık segmentler hakkında iyi bir açıklama sağlar.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Yönlendirme, istek URI 'Lerini rota işleyicileriyle eşleştirmekten ve gelen istekleri gönderen sorumludur. Yollar uygulamada tanımlanır ve uygulama başlatıldığında yapılandırılır. Yol, isteğe bağlı olarak istekte bulunan URL 'den değerleri ayıklayabilir ve bu değerler, istek işleme için kullanılabilir. Uygulamadan yapılandırılan yollar kullanıldığında, yönlendirme, yol işleyicileriyle eşlenen URL 'Ler oluşturabilir.

ASP.NET Core 2,1 ' de en son yönlendirme senaryolarını kullanmak için, ' de MVC Hizmetleri kaydının [Uyumluluk sürümünü](xref:mvc/compatibility-version) belirtin `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Bu belge, alt düzey ASP.NET Core yönlendirmeyi içerir. ASP.NET Core MVC yönlendirme hakkında bilgi için bkz <xref:mvc/controllers/routing> .. Sayfalardaki yönlendirme kuralları hakkında daha fazla bilgi için Razor bkz <xref:razor-pages/razor-pages-conventions> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Yönlendirme temelleri

Çoğu uygulama, URL 'Lerin okunabilir ve anlamlı olması için temel ve açıklayıcı bir yönlendirme şeması seçmelidir. Varsayılan geleneksel yol `{controller=Home}/{action=Index}/{id?}` :

* Temel ve açıklayıcı bir yönlendirme düzenini destekler.
* , UI tabanlı uygulamalar için kullanışlı bir başlangıç noktasıdır.

Geliştiriciler, [öznitelik yönlendirme](xref:mvc/controllers/routing#attribute-routing) veya adanmış geleneksel yollar kullanarak özel durumlarda (örneğin, blog ve e-ticaret uç noktaları) bir uygulamanın yüksek trafik bölümlerine daha fazla terse yolu ekler.

Web API 'Leri, uygulamanın işlevselliğini HTTP fiilleri tarafından temsil edilen bir kaynak kümesi olarak modellemek için öznitelik yönlendirmeyi kullanmalıdır. Bu, aynı mantıksal kaynaktaki birçok işlemin (örneğin, GET, POST) aynı URL 'YI kullanacağı anlamına gelir. Öznitelik yönlendirme, bir API 'nin Genel uç nokta yerleşimini dikkatle tasarlamak için gereken bir denetim düzeyi sağlar.

Razor Sayfalar uygulamalar, bir uygulamanın *Sayfalar* klasöründe adlandırılmış kaynaklara hizmeti sağlamak için varsayılan geleneksel yönlendirmeyi kullanır. Sayfa yönlendirme davranışını özelleştirmenizi sağlayan ek kurallar mevcuttur Razor . Daha fazla bilgi için <xref:razor-pages/index> ve <xref:razor-pages/razor-pages-conventions> bölümlerine bakın.

URL oluşturma desteği, uygulamanın, uygulamayı birbirine bağlamak için sabit kodlama URL 'Leri olmadan geliştirilebilmesine izin verir. Bu destek, temel bir yönlendirme yapılandırmasıyla başlayıp uygulamanın kaynak düzeni belirlendikten sonra yolların değiştirilmesini sağlar.

Yönlendirme, ' nin rotalar uygulamalarını kullanır <xref:Microsoft.AspNetCore.Routing.IRouter> :

* Gelen istekleri *Rota işleyicilerine*eşleyin.
* Yanıtlarda kullanılan URL 'Leri oluşturun.

Varsayılan olarak, bir uygulama tek bir yollar koleksiyonuna sahiptir. Bir istek geldiğinde koleksiyondaki yollar, koleksiyonda var olan sırada işlenir. Çerçeve koleksiyondaki her bir rotada yöntemini çağırarak, bir gelen istek URL 'sini koleksiyondaki bir yola eşleştirmeye çalışır <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> . Yanıt, yönlendirme bilgilerine göre URL (örneğin, yeniden yönlendirme veya bağlantılar için) oluşturmak için yönlendirmeyi kullanabilir ve bu sayede bakım yapılmasına yardımcı olan sabit kodlanmış URL 'Lerden kaçınabilirsiniz.

Yönlendirme sistemi aşağıdaki özelliklere sahiptir:

* Yol şablonu sözdizimi, simgeleştirilmiş yol parametrelerine sahip yolları tanımlamak için kullanılır.
* Geleneksel stil ve öznitelik stili uç nokta yapılandırmasına izin verilir.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> bir URL parametresinin belirli bir uç nokta kısıtlaması için geçerli bir değer içerip içermediğini belirlemekte kullanılır.
* MVC/sayfalar gibi uygulama modelleri, Razor yönlendirme senaryolarının öngörülebilir bir uygulaması olan tüm yollarını kaydeder.
* Yanıt, yönlendirme bilgilerine göre URL (örneğin, yeniden yönlendirme veya bağlantılar için) oluşturmak için yönlendirmeyi kullanabilir ve bu sayede bakım yapılmasına yardımcı olan sabit kodlanmış URL 'Lerden kaçınabilirsiniz.
* URL oluşturma, rastgele genişletilebilirliği destekleyen yollara dayalıdır. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> URL 'Ler oluşturmak için yöntemler sunar.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Yönlendirme, sınıfı tarafından bulunan [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzenine bağlıdır <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> . [ASP.NET Core MVC](xref:mvc/overview) , yapılandırma kapsamında bir ara yazılım ardışık düzenine yönlendirme ekler ve MVC ve sayfalar uygulamalarında yönlendirmeyi işler Razor . Tek başına bileşen olarak yönlendirmeyi nasıl kullanacağınızı öğrenmek için [yönlendirme ara yazılımı kullanma](#use-routing-middleware) bölümüne bakın.

### <a name="url-matching"></a>URL eşleştirme

URL eşleştirme, yönlendirmenin bir *işleyiciye*gelen istek gönderdiğine yönelik işlemdir. Bu işlem, URL yolundaki verileri temel alır, ancak istekteki verileri göz önünde bulundurmanız için genişletilebilir. Ayrı işleyicilere istek gönderme özelliği, bir uygulamanın boyutunu ve karmaşıklığını ölçeklendirmeye yönelik anahtardır.

Gelen istekler, <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> sırayla her bir yoldaki yöntemi çağıran öğesini girer. <xref:Microsoft.AspNetCore.Routing.IRouter>Örnek, [routecontext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) değerini null olmayan olarak ayarlayarak isteğin *işleneceğini* seçer <xref:Microsoft.AspNetCore.Http.RequestDelegate> . Bir yol istek için bir işleyici ayarlarsa, yol işleme duraklar ve işleyici isteği işlemek için çağrılır. İsteği işlemek için yol işleyicisi bulunmazsa, ara yazılım istek ardışık düzeninde sonraki bir ara yazılım için isteği kapatır.

' Ye birincil giriş, <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> geçerli istekle Ilişkili [routecontext. HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) ' dir. [Routecontext. Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) ve [Routecontext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) , bir yol eşleştirdikten sonra çıkış olarak ayarlanır.

Çağıran bir eşleşme <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> de [routecontext. RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) özelliklerinin özelliklerini, bu nedenle yapılan istek işlemeye bağlı olarak uygun değerlere ayarlar.

[RouteData. Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) , rotada oluşturulan *yol değerlerinin* bir sözlüğüdür. Bu değerler genellikle URL 'YI simgeleştirerek belirlenir ve Kullanıcı girişini kabul etmek ya da uygulama içinde daha fazla kararlar almak için kullanılabilir.

[RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) , eşleşen rotayla ilgili ek verilerin bir özellik çantadır. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> Her rotayla durum verilerinin ilişkilendirilmesini desteklemek için sağlanır, böylece uygulama hangi yolun eşleştiğini temel alarak kararlar alabilir. Bu değerler, geliştirici tarafından tanımlanır ve yönlendirme davranışını herhangi bir **şekilde etkilemez.** Ayrıca, [RouteData. DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) içinde bulunan değerler her türlü türden olabilir. Bu, [veri](xref:Microsoft.AspNetCore.Routing.RouteData.Values)dizeleri arasında dönüştürülebilir olmalıdır.

[RouteData. yönlendiriciler](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) , isteği başarıyla eşleştirirken geçen yolların bir listesidir. Yollar bir diğerinin içinde iç içe olabilir. <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>Özelliği, bir eşleşme ile sonuçlanan yolların mantıksal ağacı aracılığıyla yolu yansıtır. Genellikle, içindeki ilk öğe <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> yol koleksiyonudur ve URL oluşturma için kullanılmalıdır. İçindeki son öğe, <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> eşleşen yol işleyicisidir.

<a name="lg"></a>

### <a name="url-generation"></a>URL oluşturma

URL oluşturma, yönlendirmenin bir yol değerleri kümesine göre bir URL yolu oluşturabileceği işlemdir. Bu, yönlendirme işleyicileri ve bunlara erişen URL 'Ler arasındaki mantıksal bir ayrım sağlar.

URL oluşturma benzer bir yinelemeli işlem izler, ancak yol koleksiyonu metoduna çağıran kullanıcı veya çerçeve kodu ile başlar <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> . Her *yol* , <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> null olmayan bir değer döndürülene kadar metodu sırayla çağırılır <xref:Microsoft.AspNetCore.Routing.VirtualPathData> .

Birincil girdileri <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> :

* [VirtualPathContext. HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext. Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext. AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Yollar birincil olarak, tarafından belirtilen yol değerlerini kullanır ve <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> bir URL oluşturma ve hangi değerlerin dahil edileceğini belirlemek için. , <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> Geçerli istekle eşleştirmeden üretilmiş olan rota değerleri kümesidir. Buna karşılık, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> geçerli işlem için Istenen URL 'nin nasıl oluşturulacağını belirten rota değerlerdir. , <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> Bir yolun geçerli bağlamla ilişkili hizmetleri veya ek verileri alması durumunda sağlanır.

> [!TIP]
> Virtualpathcontext [. Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) öğesini [Virtualpathcontext. AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*)için bir geçersiz kılma kümesi olarak düşünün. URL oluşturma, aynı rota veya yol değerlerini kullanan bağlantılar için URL 'Ler oluşturmak üzere geçerli istekten yol değerlerini yeniden kullanmayı dener.

Çıkışı <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> bir <xref:Microsoft.AspNetCore.Routing.VirtualPathData> . <xref:Microsoft.AspNetCore.Routing.VirtualPathData> , öğesinin bir paraleldir <xref:Microsoft.AspNetCore.Routing.RouteData> . <xref:Microsoft.AspNetCore.Routing.VirtualPathData><xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>Çıkış URL 'si için ve yol tarafından ayarlanması gereken bazı ek özellikler içerir.

[VirtualPathData. VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) özelliği, yol tarafından üretilen *sanal yolu* içerir. Gereksinimlerinize bağlı olarak, yolu daha fazla işlem yapmanız gerekebilir. Oluşturulan URL 'YI HTML 'de işlemek istiyorsanız, uygulamanın temel yolunu ekleyin.

[VirtualPathData. Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) , URL 'yi başarıyla oluşturmuş olan yola bir başvurudur.

[VirtualPathData. DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) özellikleri, URL 'yi oluşturan rotayla ilgili ek verilerin bir sözlüğüdür. Bu, [RouteData. Datatoken](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*)'ların paraleldir.

### <a name="create-routes"></a>Yolları oluşturma

Yönlendirme <xref:Microsoft.AspNetCore.Routing.Route> sınıfı sınıfının standart uygulamasını sağlar <xref:Microsoft.AspNetCore.Routing.IRouter> . <xref:Microsoft.AspNetCore.Routing.Route> çağrıldığında URL yoluyla eşleştirilecek desenleri tanımlamak için *yol şablonu* sözdizimini kullanır <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> . <xref:Microsoft.AspNetCore.Routing.Route> çağrıldığında bir URL oluşturmak için aynı rota şablonunu kullanır <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> .

Çoğu uygulama, <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ' de tanımlanan benzer uzantı yöntemlerinden birini çağırarak veya arayarak yollar oluşturur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> . <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>Uzantı yöntemlerinden herhangi biri bir örneği oluşturur <xref:Microsoft.AspNetCore.Routing.Route> ve bunu yol koleksiyonuna ekler.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> yol işleyici parametresini kabul etmez. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> yalnızca tarafından işlenen rotaları ekler <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> . Varsayılan işleyici bir ' dir `IRouter` ve işleyici isteği işleyemeyebilir. Örneğin, ASP.NET Core MVC genellikle yalnızca kullanılabilir bir denetleyici ve eylemle eşleşen istekleri işleyen varsayılan bir işleyici olarak yapılandırılır. MVC 'de yönlendirme hakkında daha fazla bilgi için bkz <xref:mvc/controllers/routing> ..

Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> tipik bir ASP.NET Core MVC yol tanımı tarafından kullanılan bir çağrının örneğidir:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Bu şablon bir URL yoluyla eşleşir ve yol değerlerini ayıklar. Örneğin, yol `/Products/Details/17` Şu yol değerlerini oluşturur: `{ controller = Products, action = Details, id = 17 }` .

Rota değerleri, URL yolunun kesimlere bölünüyor ve rota şablonundaki *yol parametresi* adı ile her bir segmenti eşleştirerek belirlenir. Yol parametreleri olarak adlandırılır. Küme ayraçları içinde parametre adının çevreleme tarafından tanımlanan parametreler `{ ... }` .

Yukarıdaki şablon URL yoluyla da eşleştirebilir `/` ve değerleri üretebilir `{ controller = Home, action = Index }` . Bu durum, `{controller}` ve `{action}` Rota parametrelerinin varsayılan değerleri olduğu ve `id` route parametresinin isteğe bağlı olması nedeniyle oluşur. Bir eşittir işareti ( `=` ), yol parametre adından sonra gelen bir değer, parametre için varsayılan bir değer tanımlar. `?`Yol parametre adından sonra bir soru işareti () isteğe bağlı bir parametre tanımlar.

Yol parametreleri varsayılan değer ile *her zaman* rota değeri oluşturur. İsteğe bağlı parametreler, karşılık gelen bir URL yol kesimi yoksa rota değeri oluşturmaz. Yol şablonu senaryolarının ve sözdiziminin kapsamlı bir açıklaması için [yol şablonu başvurusu](#route-template-reference) bölümüne bakın.

Aşağıdaki örnekte, Route parametresi tanımı `{id:int}` yol parametresi için bir [yol kısıtlaması](#route-constraint-reference) tanımlar `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Bu şablon, gibi bir URL yoluyla eşleşir `/Products/Details/17` `/Products/Details/Apples` . Yol kısıtlamaları <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , yol değerlerini uygulayıp onları doğrulamak için inceler. Bu örnekte, yol değeri `id` bir tamsayıya dönüştürülebilir olmalıdır. Framework tarafından sunulan yol kısıtlamalarının açıklaması için bkz. [route-Constraint-Reference](#route-constraint-reference) .

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>, Ve için değer kabul etmenin ek aşırı yüklemeleri `constraints` `dataTokens` `defaults` . Bu parametrelerin tipik kullanımı, anonim türdeki özellik adlarının yol parametre adlarıyla eşleşen anonim olarak yazılmış bir nesneyi geçirmektir.

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
> Kısıtlamaları ve Varsayılanları tanımlamaya yönelik satır içi sözdizimi basit yollar için kullanışlı olabilir. Ancak, satır içi sözdizimi tarafından desteklenmeyen veri belirteçleri gibi senaryolar vardır.

Aşağıdaki örnek birkaç ek senaryoyu göstermektedir:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Önceki şablon, benzer bir URL yoluyla eşleşir `/Blog/All-About-Routing/Introduction` ve değerleri ayıklar `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` . Ve için varsayılan yol değerleri `controller` , `action` şablonda karşılık gelen hiçbir yol parametresi olmasa bile rota tarafından üretilir. Varsayılan değerler yol şablonunda belirtilebilir. `article`Route parametresi, yol parametre adından önce bir yıldız işareti () görünümüne göre *catch-all* olarak tanımlanır `*` . Catch-all yol parametreleri, URL yolunun kalanını yakalar ve boş dizeyle de aynı olabilir.

Aşağıdaki örnek yol kısıtlamalarını ve veri belirteçlerini ekler:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Önceki şablon, benzer bir URL yoluyla eşleşir `/en-US/Products/5` ve değerleri `{ controller = Products, action = Details, id = 5 }` ve veri belirteçlerini ayıklar `{ locale = en-US }` .

![Yereller Windows belirteçleri](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Yol sınıfı URL 'SI oluşturma

Sınıf, yol <xref:Microsoft.AspNetCore.Routing.Route> değerlerini bir kümesini rota şablonuyla BIRLEŞTIREREK URL oluşturma işlemi de gerçekleştirebilir. Bu, URL yolunu eşleştirmenin mantıksal bir işlemdir.

> [!TIP]
> URL oluşturmayı daha iyi anlamak için, oluşturmak istediğiniz URL 'YI düşünün ve sonra bir yönlendirme şablonunun bu URL ile nasıl eşleşeceğini düşünün. Hangi değerler üretilemidir? Bu, URL oluşturma işlevinin sınıfında nasıl çalıştığına ilişkin kaba bir eştir <xref:Microsoft.AspNetCore.Routing.Route> .

Aşağıdaki örnek genel ASP.NET Core MVC varsayılan yolunu kullanır:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Yol değerleriyle `{ controller = Products, action = List }` , URL `/Products/List` oluşturulur. Yol değerleri, URL yolunu biçimlendirmek için karşılık gelen yol parametrelerinin yerine kullanılır. `id`, İsteğe bağlı bir yol parametresi olduğundan, URL 'si için bir değer olmadan başarıyla oluşturulur `id` .

Yol değerleriyle `{ controller = Home, action = Index }` , URL `/` oluşturulur. Belirtilen yol değerleri varsayılan değerlerle eşleşir ve varsayılan değerlere karşılık gelen segmentler güvenle atlanır.

Her iki URL de aşağıdaki yol tanımıyla (ve) birlikte gidiş dönüş, `/Home/Index` `/` URL oluşturmak için kullanılan rota değerlerini oluşturur.

> [!NOTE]
> ASP.NET Core MVC kullanan bir uygulama <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> , doğrudan yönlendirmeye çağırmak yerine URL 'ler oluşturmak için kullanılmalıdır.

URL oluşturma hakkında daha fazla bilgi için [URL oluşturma başvurusu](#url-generation-reference) bölümüne bakın.

## <a name="use-routing-middleware"></a>Yönlendirme ara yazılımı kullanma

Uygulamanın proje dosyasındaki [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) öğesine başvurun.

İçindeki hizmet kapsayıcısına yönlendirme Ekle `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Yolların yönteminde yapılandırılması gerekir `Startup.Configure` . Örnek uygulama aşağıdaki API 'Leri kullanır:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Yalnızca HTTP GET isteklerini eşleştirir.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Aşağıdaki tabloda verilen URI 'Ler ile ilgili yanıtlar gösterilmektedir.

| URI                    | Yanıt                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Herkese! Rota değerleri: [işlem, oluşturma], [kimlik, 3] |
| `/package/track/-3`    | Herkese! Rota değerleri: [işlem, izleme], [kimlik,-3] |
| `/package/track/-3/`   | Herkese! Rota değerleri: [işlem, izleme], [kimlik,-3] |
| `/package/track/`      | İstek, eşleşme olmadan üzerinden yapılır.              |
| `GET /hello/Joe`       | Merhaba, ali!                                          |
| `POST /hello/Joe`      | İstek üzerinden geçer, yalnızca HTTP GET ile eşleşir. |
| `GET /hello/Joe/Smith` | İstek, eşleşme olmadan üzerinden yapılır.              |

Tek bir yol yapılandırıyorsanız, <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> bir örneği geçirme çağrısı yapın `IRouter` . Kullanmanız gerekmez <xref:Microsoft.AspNetCore.Routing.RouteBuilder> .

Framework, yollar () oluşturmak için bir genişletme yöntemleri kümesi sağlar <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions> :

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

Gibi listelenen yöntemlerin bazıları için <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> gerekir <xref:Microsoft.AspNetCore.Http.RequestDelegate> . <xref:Microsoft.AspNetCore.Http.RequestDelegate>Yol eşleştiğinde *yol işleyicisi* olarak kullanılır. Bu ailedeki diğer yöntemler, yönlendirme işleyicisi olarak kullanılmak üzere bir ara yazılım ardışık düzeni yapılandırmaya olanak tanır. Yöntemi gibi `Map*` bir işleyiciyi kabul etmez, <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> öğesini kullanır <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> .

`Map[Verb]`Yöntemler, yöntem ADıNDAKI http fiili ile rotayı sınırlandırmak için kısıtlamalar kullanır. Örnek için bkz. <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> ve <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Rota şablonu başvurusu

Küme ayraçları () içindeki belirteçler `{ ... }` , yol eşleştirildiği takdirde bağlanan *Rota parametrelerini* tanımlar. Bir yol segmentinde birden fazla yol parametresi tanımlayabilirsiniz, ancak bunların bir değişmez değer ile ayrılması gerekir. Örneğin, `{controller=Home}{action=Index}` ve arasında değişmez değer olmadığından geçerli bir yol değil `{controller}` `{action}` . Bu rota parametrelerinin bir adı olmalı ve ek öznitelikler belirtilmiş olabilir.

Yol parametrelerinden (örneğin, `{id}` ) ve yol ayırıcısından farklı bir metin, `/` URL içindeki metinle eşleşmelidir. Metin eşleştirme, büyük/küçük harfe duyarsızdır ve URL yolunun kodu çözülmüş gösterimine göre yapılır. Bir sabit yol parametresi sınırlayıcısından ( `{` veya) eşleştirmek için `}` , karakteri (veya) tekrarlayarak sınırlayıcıdan kaçış `{{` `}}` .

İsteğe bağlı bir dosya uzantısına sahip bir dosya adı yakalamaya deneyen URL desenlerinin ek konuları vardır. Örneğin, şablonu göz önünde bulundurun `files/{filename}.{ext?}` . Hem hem de için değerler olduğunda `filename` `ext` her iki değer de doldurulur. URL 'de yalnızca bir değeri `filename` varsa, sondaki nokta ( `.` ) isteğe bağlı olduğundan yol eşleşir. Aşağıdaki URL 'Ler bu rota ile eşleşiyor:

* `/files/myFile.txt`
* `/files/myFile`

`*`URI 'nin geri kalanına bağlamak için bir yol parametresinin öneki olarak yıldız işareti () kullanabilirsiniz. Buna *catch-all* parametresi denir. Örneğin, `blog/{*slug}` ile başlayan `/blog` ve bunu izleyen bir değere sahip olan ve yol değerine atanan HERHANGI bir URI ile eşleşir `slug` . Catch-all parametreleri boş dizeyle de aynı olabilir.

Yol ayırıcısı () karakterleri de dahil olmak üzere bir URL oluşturmak için, catch-all parametresi uygun karakterleri de çıkar `/` . Örneğin, `foo/{*path}` yol değerlerini içeren yol `{ path = "my/path" }` oluşturulur `foo/my%2Fpath` . Atlanan eğik çizgiye göz önünde edin.

Yol parametreleri, parametre adından sonra bir eşittir işaretiyle () ayırarak varsayılan değer belirtilerek belirlenmiş *varsayılan değerlere* sahip olabilir `=` . Örneğin, `{controller=Home}` `Home` için varsayılan değer olarak tanımlar `controller` . Parametresi için URL 'de hiçbir değer yoksa varsayılan değer kullanılır. Yol parametreleri `?` , içinde olduğu gibi parametre adının sonuna bir soru işareti () eklenerek isteğe bağlı olarak yapılır `id?` . İsteğe bağlı değerler ve varsayılan yol parametreleri arasındaki fark, varsayılan değere sahip bir yol parametresinin her zaman bir değer ürettiğinden, &mdash; isteğe bağlı bir parametre yalnızca Istek URL 'si tarafından bir değer sağlandığı zaman bir değere sahip olur.

Rota parametrelerinin URL 'den bağlanan rota değeriyle eşleşmesi gereken kısıtlamaları olabilir. `:`Yol parametre adından sonra bir iki nokta () ve kısıtlama adı eklemek, bir rota parametresinde bir *satır içi kısıtlamayı* belirtir. Kısıtlama bağımsız değişkenler gerektiriyorsa, `(...)` kısıtlama adından sonra parantez () içine alınır. Birden çok satır içi kısıtlama, başka bir iki nokta ( `:` ) ve kısıtlama adı eklenerek belirtilebilir.

Kısıtlama adı ve bağımsız değişkenler, <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> URL işlemede kullanılmak üzere bir örneği oluşturmak için hizmetine geçirilir. Örneğin, yol şablonu `blog/{article:minlength(10)}` `minlength` bağımsız değişkenle bir kısıtlama belirtir `10` . Yol kısıtlamaları ve Framework tarafından sunulan kısıtlamaların bir listesi hakkında daha fazla bilgi için, [route kısıtlama başvurusu](#route-constraint-reference) bölümüne bakın.

Aşağıdaki tabloda örnek yol şablonları ve bunların davranışları gösterilmektedir.

| Rota şablonu                           | Örnek eşleşen URI    | İstek URI 'SI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Yalnızca tek bir yolla eşleşir `/hello` .                                     |
| `{Page=Home}`                            | `/`                     | İle eşleşir ve `Page` ayarlanır `Home` .                                         |
| `{Page=Home}`                            | `/Contact`              | İle eşleşir ve `Page` ayarlanır `Contact` .                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | `Products`Denetleyiciye ve `List` eyleme eşlenir.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products`Denetleyici ve `Details` eyleme eşlenir ( `id` 123 olarak ayarlanır). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home`Denetleyiciye ve `Index` yönteme eşlenir ( `id` yok sayılır).        |

Bir şablon kullanmak genellikle yönlendirmeye en basit yaklaşımdır. Kısıtlamalar ve varsayılanlar, yol şablonu dışında da belirtilebilir.

> [!TIP]
> İstekleri eşleme gibi yerleşik yönlendirme uygulamalarının nasıl yapıldığını görmek için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin <xref:Microsoft.AspNetCore.Routing.Route> .

## <a name="route-constraint-reference"></a>Yol kısıtlama başvurusu

Yol kısıtlamaları, gelen URL 'de bir eşleşme meydana geldiğinde ve URL yolu yol değerlerinde simgeleştirilir yürütülür. Rota kısıtlamaları genellikle yol şablonu aracılığıyla ilişkili rota değerini inceler ve değerin kabul edilebilir olup olmadığı konusunda bir Evet/Hayır kararı getirir. Bazı rota kısıtlamaları, isteğin yönlendirilip yönlendirilmeyeceğini göz önünde bulundurmanız için yol değeri dışındaki verileri kullanır. Örneğin, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> bir ISTEğI http fiiline bağlı olarak kabul edebilir veya reddedebilir. Kısıtlamalar, yönlendirme isteklerinde ve bağlantı oluşturmada kullanılır.

> [!WARNING]
> **Giriş doğrulaması**için kısıtlamaları kullanmayın. **Giriş doğrulaması**için kısıtlamalar kullanılıyorsa, doğru bir hata iletisine sahip *400-Bad isteği* yerine *404-* olmayan bir Yanıt ile geçersiz giriş oluşur. Yol kısıtlamaları, belirli bir rota için girdileri doğrulamak üzere değil, benzer yolların **belirsizliğini ortadan** kaldırmak için kullanılır.

Aşağıdaki tabloda örnek yol kısıtlamaları ve bunların beklenen davranışları gösterilmektedir.

| kısıtlama | Örnek | Örnek eşleşmeler | Notlar |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Herhangi bir tamsayıyla eşleşir |
| `bool` | `{active:bool}` | `true`, `FALSE` | Eşleşiyor `true` veya `false` (büyük/küçük harf duyarsız) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | `DateTime`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | `decimal`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | `double`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | `float`Sabit kültürün geçerli bir değeriyle eşleşir. Önceki uyarıya bakın.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Geçerli bir `Guid` değerle eşleşir |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Geçerli bir `long` değerle eşleşir |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Dize en az 4 karakter olmalıdır |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Dize 8 karakterden uzun olmamalıdır |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Dize tam olarak 12 karakter uzunluğunda olmalıdır |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Dize en az 8 ve en fazla 16 karakter uzunluğunda olmalıdır |
| `min(value)` | `{age:min(18)}` | `19` | Tamsayı değeri en az 18 olmalıdır |
| `max(value)` | `{age:max(120)}` | `91` | Tamsayı değeri 120 ' ten fazla olmamalıdır |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Tamsayı değeri en az 18 olmalı ancak 120 ' ten fazla olmamalıdır |
| `alpha` | `{name:alpha}` | `Rick` | Dize bir veya daha fazla alfabetik karakterden oluşmalıdır ( `a` - `z` büyük/küçük harfe duyarsız) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Dize, normal ifadeyle eşleşmelidir (normal ifade tanımlama hakkında ipuçlarına bakın) |
| `required` | `{name:required}` | `Rick` | URL oluşturma sırasında parametre olmayan bir değerin mevcut olduğunu zorlamak için kullanılır |

Birden çok, iki nokta üst üste sınırlı kısıtlama tek bir parametreye uygulanabilir. Örneğin, aşağıdaki kısıtlama bir parametreyi 1 veya daha büyük bir tamsayı değeriyle kısıtlar:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> URL 'YI doğrulayan ve bir CLR türüne (veya gibi) dönüştürülen yol kısıtlamaları `int` `DateTime` , her zaman sabit kültürü kullanır. Bu kısıtlamalar, URL 'nin yerelleştirilemeyen olduğunu varsayar. Framework tarafından sunulan yol kısıtlamaları, yol değerlerinde depolanan değerleri değiştirmez. URL 'den Ayrıştırılan tüm rota değerleri dizeler olarak depolanır. Örneğin, `float` kısıtlama yol değerini bir float öğesine dönüştürmeye çalışır, ancak dönüştürülen değer yalnızca bir float öğesine dönüştürülebileceğini doğrulamak için kullanılır.

## <a name="regular-expressions"></a>Normal ifadeler

ASP.NET Core Framework, `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` normal ifade oluşturucusuna ekler. <xref:System.Text.RegularExpressions.RegexOptions>Bu üyelerin açıklaması için bkz..

Normal ifadeler, Yönlendirme ve C# dili tarafından kullanılanlarla benzerlik olan sınırlayıcıları ve belirteçleri kullanır. Normal ifade belirteçlerinin atlanmalıdır. Yönlendirmenin normal ifadesini kullanmak için `^\d{3}-\d{2}-\d{4}$` , ifadenin `\` `\\` `\` dize kaçış karakterini (tam [dize sabit değerleri](/dotnet/csharp/language-reference/keywords/string)kullanmadıkça) atlamak için C# kaynak dosyasındaki dize (çift ters eğik çizgi) karakterleriyle belirtilen (tek ters eğik çizgi) karakterlere sahip olması gerekir. Yönlendirme parametresi sınırlayıcı karakterlerini ( `{` ,,, `}` ) atlamak için `[` `]` , ifadedeki karakterleri (,,, `{{` ) çift `}` `[[` `]]` . Aşağıdaki tabloda, bir normal ifade ve kaçan sürümü gösterilmektedir.

| Normal ifade    | Kaçan normal Ifade     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Yönlendirmelerde kullanılan normal ifadeler, genellikle şapka işareti ( `^` ) karakteriyle başlar ve dizenin başlangıç konumuyla eşleşir. İfadeler, genellikle dolar işareti ( `$` ) karakteriyle biter ve dizenin sonuyla eşleşir. `^`Ve `$` karakterleri, normal ifadenin tüm yol parametresi değeri ile eşleştiğinden emin olun. `^`Ve karakterleri olmadan `$` normal ifade, dize içindeki herhangi bir alt dizeden eşleşir ve bu genellikle istenmeyen bir ifadedir. Aşağıdaki tabloda örnekler verilmektedir ve bunların eşleşmesinin neden eşleşmediği veya eşleşmemesi açıklanmaktadır.

| Expression   | Dize    | Eşleştirme | Yorum               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | 123abc456 | Yes   | Alt dize eşleşmeleri     |
| `[a-z]{2}`   | MZ        | Yes   | Eşleşen ifadesi    |
| `[a-z]{2}`   | MZ        | Yes   | Büyük/küçük harfe duyarlı değil    |
| `^[a-z]{2}$` | hello     | Hayır    | Bkz. `^` ve `$` üzeri |
| `^[a-z]{2}$` | 123abc456 | Hayır    | Bkz. `^` ve `$` üzeri |

Normal ifade sözdizimi hakkında daha fazla bilgi için bkz. [.NET Framework normal ifadeler](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Bir parametreyi bilinen olası değerler kümesiyle kısıtlamak için, normal bir ifade kullanın. Örneğin, `{action:regex(^(list|get|create)$)}` yalnızca `action` Rota değeri `list` ,, veya ile eşleşir `get` `create` . Kısıtlama sözlüğüne geçirilirse dize `^(list|get|create)$` eşdeğerdir. Bilinen kısıtlamalardan biriyle eşleşmeyen kısıtlama sözlüğüne (bir şablon içinde satır içi değil) geçirilen kısıtlamalar da normal ifadeler olarak kabul edilir.

## <a name="custom-route-constraints"></a>Özel yol kısıtlamaları

Yerleşik yol kısıtlamalarına ek olarak, arabirimi uygulayarak özel yol kısıtlamaları oluşturulabilir <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> . <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>Arabirim, `Match` `true` kısıtlama karşılanıp Aksi takdirde döndüren tek bir yöntemi içerir `false` .

Özel bir kullanmak için <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> yol kısıtlama türü, uygulamanın uygulamanın hizmet kapsayıcısında kayıtlı olması gerekir <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> . <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>, Yol kısıtlama anahtarlarını <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> Bu kısıtlamaları doğrulayan uygulamalarla eşleyen bir sözlüktür. Bir uygulama <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> , `Startup.ConfigureServices` hizmetlerin bir parçası olarak ' de güncelleştirilir [. AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) çağrısı veya <xref:Microsoft.AspNetCore.Routing.RouteOptions> doğrudan ile yapılandırma `services.Configure<RouteOptions>` . Örneğin:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Kısıtlama daha sonra, kısıtlama türü kaydedilirken belirtilen ad kullanılarak yollara her zamanki şekilde uygulanabilir. Örneğin:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>URL oluşturma başvurusu

Aşağıdaki örnek, yol değerlerinin ve bir sözlüğünün bir sözlüğüne verilen bir yolun bağlantısının nasıl oluşturulacağını gösterir <xref:Microsoft.AspNetCore.Routing.RouteCollection> .

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>Önceki örnek sonunda oluşturulan `/package/create/123` . Sözlük, `operation` `id` "paket yolunu izle" şablonunun ve rota değerlerini sağlar `package/{operation}/{id}` . Ayrıntılar için, [yönlendirme ara yazılımı kullanma](#use-routing-middleware) bölümünde veya [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)örnek koda bakın.

Oluşturucunun ikinci parametresi <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> bir *ortam değerleri*koleksiyonudur. Ortam değerleri, bir geliştiricinin bir istek bağlamı içinde belirtmesi gereken değer sayısını sınırlandırdığından kullanım için uygundur. Geçerli isteğin geçerli yol değerleri, bağlantı oluşturma için çevresel değerler olarak kabul edilir. ASP.NET Core MVC uygulamasının `About` öğesinin eyleminde, `HomeController` `Index` &mdash; ortam değeri kullanılan eyleme bağlamak için denetleyici yol değerini belirtmeniz gerekmez `Home` .

Bir parametreyle eşleşmeyen çevresel değerler yok sayılır. Ayrıca, açıkça sağlanmış bir değer çevresel değeri geçersiz kıldığında çevresel değerler de yoksayılır. Eşleştirme, URL 'de soldan sağa doğru gerçekleşir.

Açık olarak sağlanmış ancak yolun bir segmentiyle eşleşmeyen değerler sorgu dizesine eklenir. Aşağıdaki tabloda, yol şablonu kullanılırken sonuç gösterilmektedir `{controller}/{action}/{id?}` .

| Çevresel değerler                     | Açık değerler                        | Sonuç                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| denetleyici = "giriş"                | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | denetleyici = "Order", Action = "hakkında" | `/Order/About`          |
| denetleyici = "giriş", renk = "kırmızı" | Action = "hakkında"                       | `/Home/About`           |
| denetleyici = "giriş"                | Action = "hakkında", color = "Red"        | `/Home/About?color=Red` |

Bir rotada bir parametreye karşılık gelen bir varsayılan değer varsa ve bu değer açıkça sağlanmışsa, varsayılan değerle eşleşmelidir:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Bağlantı oluşturma yalnızca ve için eşleşen değerler sağlandığında bu yol için bir bağlantı oluşturur `controller` `action` .

## <a name="complex-segments"></a>Karmaşık segmentler

Karmaşık segmentler (örneğin `[Route("/x{token}y")]` ), sabit değerli olmayan değişmez değer ile sağdan sola eşleştirilirken işlenir. Karmaşık segmentlerin nasıl eşleştirileceği hakkında ayrıntılı bir açıklama için [bu koda](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) bakın. [Kod örneği](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) ASP.NET Core tarafından kullanılmaz, ancak karmaşık segmentler hakkında iyi bir açıklama sağlar.

::: moniker-end
