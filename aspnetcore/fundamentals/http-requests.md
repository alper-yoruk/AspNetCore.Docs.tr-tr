---
title: ASP.NET Core'da IHttpClientFactory kullanarak HTTP isteklerini gerçekleştirin
author: stevejgordon
description: ASP.NET Core'da mantıksal HttpClient örneklerini yönetmek için IHttpClientFactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 912be34ae0ee25837a94aab65443f15b17ab4556
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661688"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core'da IHttpClientFactory kullanarak HTTP isteklerini gerçekleştirin

::: moniker range=">= aspnetcore-3.0"

Glenn [Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), Rick [Anderson](https://twitter.com/RickAndMSFT), ve [Kirk Larkin](https://github.com/serpent5) tarafından

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir. `IHttpClientFactory`aşağıdaki avantajları sunar:

* Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *github* adlı bir istemci kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir. Varsayılan istemci genel erişim için kaydedilebilir.
* Giden ara yazılım kavramını işleyicileri 'nde `HttpClient`atayarak kodlar. Polly tabanlı ara yazılım için uzantılar `HttpClient`sağlar.
* Temel `HttpClientMessageHandler` örneklerin havuzlama ve ömür boyu yönetir. Otomatik yönetim, yaşam ömürlerini el ile yönetirken `HttpClient` oluşan yaygın DNS (Etki Alanı Adı Sistemi) sorunlarını önler.
* Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample)

Bu konu sürümündeki örnek <xref:System.Text.Json> kod, HTTP yanıtlarında döndürülen JSON içeriğini deserialize etmek için kullanır. Bu konuyu `Json.NET` kullanan `ReadAsAsync<T>`ve sürüm seçicisini kullanan örnekler için bu konunun 2.x sürümünü seçin.

## <a name="consumption-patterns"></a>Tüketim alışkanlıkları

Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

En iyi yaklaşım uygulamanın gereksinimlerine bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory`arayarak `AddHttpClient`kaydedilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Bağımlılık `IHttpClientFactory` [enjeksiyonu (DI)](xref:fundamentals/dependency-injection)kullanılarak istenebilir. Aşağıdaki kod `IHttpClientFactory` bir `HttpClient` örnek oluşturmak için kullanır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Yukarıdaki `IHttpClientFactory` örnekte olduğu gibi kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur. Nasıl kullanılacağı üzerinde `HttpClient` hiçbir etkisi yoktur. Varolan `HttpClient` bir uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumları ' ya yapılan <xref:System.Net.Http.IHttpClientFactory.CreateClient*>çağrılarla değiştirin.

### <a name="named-clients"></a>Adlandırılmış istemciler

Adlandırılmış istemciler, şu zaman iyi bir seçimdir:

* Uygulama birçok farklı kullanım `HttpClient`gerektirir.
* Birçok `HttpClient`s farklı yapılandırma var.

Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Önceki kodda istemci aşağıdakilerle yapılandırılır:

* Temel adres. `https://api.github.com/`
* GitHub API ile çalışmak için iki üstbilgi gerekir.

#### <a name="createclient"></a>CreateClient

Her <xref:System.Net.Http.IHttpClientFactory.CreateClient*> zaman denir:

* Yeni `HttpClient` bir örnek oluşturulur.
* Yapılandırma eylemi denir.

Adlandırılmış bir istemci oluşturmak için, adını şu na `CreateClient`

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez. İstemci için yapılandırılan temel adres kullanıldığından, kod yalnızca yolu geçebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.
* Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.
* Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın. Örneğin, tek bir dakti-sa-yazılı istemci kullanılabilir:
  * Tek bir arka uç bitiş noktası için.
  * Bitiş noktası ile ilgili tüm mantık kapsüllemek için.
* DI ile çalışın ve uygulamada gerektiğinde enjekte edilebilir.

Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Yukarıdaki kodda:

* Yapılandırma, yazılan istemciye taşınır.
* Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır.

İşlevselliği ortaya çıkaran `HttpClient` API'ye özgü yöntemler oluşturulabilir. Örneğin, `GetAspNetDocsIssues` yöntem açık sorunları almak için kodu kapsüller.

Bir dakti-sa'lık istemci sınıfını kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` için aşağıdaki kod çağırır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Önceki kodda, `AddHttpClient` geçici `GitHubService` bir hizmet olarak kaydeder. Bu kayıt, bir fabrika yöntemi kullanır:

1. `HttpClient` örneği oluşturun.
1. Bir örnek `GitHubService`oluşturun , onun `HttpClient` yapıcı için örneğinde geçen.

Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Daktio istemci için yapılandırma, yazılı `Startup.ConfigureServices`istemcinin oluşturucusu yerine, kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Yazılmış `HttpClient` bir istemci içinde kapsüllenebilir. Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak çağıran bir yöntem tanımlayın:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda, özel `HttpClient` bir alanda depolanır. Erişim ortak `HttpClient` `GetRepos` yöntemle.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıkları ile birlikte kullanılabilir. Refit ,NET için bir REST kitaplığıdır. REST API'lerini canlı arabirimlere dönüştürür. Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.

Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Giden istek middleware

`HttpClient`giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır. `IHttpClientFactory`:

* Her adlandırılmış istemci için uygulamak için işleyicileri tanımlama kolaylaştırır.
* Giden istek ara yazılım ardışık oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir. Bu desen:

  * ASP.NET Core gelen middleware boru hattı benzer.
  * HTTP istekleri yle ilgili çapraz kesme endişelerini yönetmek için bir mekanizma sağlar:

    * Önbelleğe alma
    * hata işleme
    * Seri -leştirme
    * günlüğe kaydetme

Devratıcı bir işleyici oluşturmak için:

* Türetin <xref:System.Net.Http.DelegatingHandler>.
* Geçersiz <xref:System.Net.Http.DelegatingHandler.SendAsync*>kılma. İsteği ardışık alandaki bir sonraki işleyiciye geçirmeden önce kodu yürüt:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

`X-API-KEY` Üstbilgi istekte olup olmadığını önceki kod denetler. `X-API-KEY` Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.

Bir yapılandırmaya `HttpClient` birden fazla işleyici <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>eklenebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır. Her `IHttpClientFactory` işleyici için ayrı bir DI kapsamı oluşturur. İşleyiciler herhangi bir kapsamdaki hizmetlere güvenebilir. İşleyicinin bağlı olduğu hizmetler, işleyici imha edildiğinde elden çıkarılır.

Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici için tür geçen, çağrılabilir.

Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* [HttpRequestMessage.Properties'i](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak verileri işleyiciye aktarın.
* Geçerli <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> isteğe erişmek için kullanın.
* Verileri aktarmak <xref:System.Threading.AsyncLocal`1> için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyicileri kullanma

`IHttpClientFactory`üçüncü taraf kütüphane [Polly](https://github.com/App-vNext/Polly)ile entegre . Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır. Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.

Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır. Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler. Polly [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketi gerektirir.

### <a name="handle-transient-faults"></a>Geçici hataları işleme

Hatalar genellikle harici HTTP çağrıları geçici olduğunda oluşur. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>geçici hataları işlemek için bir ilke tanımlanmasına izin verir. Aşağıdaki yanıtları `AddTransientHttpErrorPolicy` işlemek ile yapılandırılan ilkeler:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy`olası geçici `PolicyBuilder` bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır. Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilkeleri seçin

Uzantı yöntemleri Polly tabanlı işleyicileri eklemek için <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>sağlanır, örneğin. Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi politikanın uygulanacağına karar verme isteğini denetler:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır. Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyicisi ekleme

Polly politikalarını yuvalamak yaygındır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Önceki örnekte:

* İki işleyici eklenir.
* İlk işleyici <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> yeniden deneme ilkesi eklemek için kullanır. Başarısız istekler en fazla üç kez yeniden denendir.
* İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler. 5 başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir. Devre kesici ilkeleri durumludur. Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilkeler ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir .

Aşağıdaki kodda:

* "Düzenli" ve "uzun" polisler eklenir.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>kayıt defterinden "düzenli" ve "uzun" ilkeleri ekler.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Polly entegrasyonları `IHttpClientFactory` hakkında daha fazla bilgi için [Polly wiki sayfasına](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)bakın.

## <a name="httpclient-and-lifetime-management"></a>Httpİste ve yaşam boyu yönetim

Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür. Adlandırılmış istemci başına bir <xref:System.Net.Http.HttpMessageHandler> oluşturulur. `HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar. Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir. Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir. Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (Etki Alanı Adı Sistemi) değişikliklerine tepki sini engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`örnekleri genellikle imha **gerektirmeyen** .NET nesneleri olarak kabul edilebilir. Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder. `IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar.

Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir. Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory için alternatifler

DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:

* Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.
* Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.

Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.

- Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.
- Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.

Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım soket yorgunluğunu önler.
- Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.

### <a name="cookies"></a>Tanımlama bilgileri

Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır. Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:

 - Otomatik çerez işlemeyi devre dışı bırakma
 - Kaçın -arak`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe kaydetme

Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlüğe kaydetme yapılandırmasında uygun bilgi düzeyini etkinleştirin. İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.

Her istemci için kullanılan günlük kategorisi istemcinin adını içerir. Örneğin *MyNamedClient*adlı bir istemci iletileri "System.Net.Http.HttpClient" kategorisiyle günlüğe kaydeder. **MyNamedClient**. MantıksalHandler". *LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur. İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir. Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur. *MyNamedClient* örneğinde, bu iletiler "System.Net.HttpClient" günlük kategorisi ile günlüğe kaydedilir. **MyNamedClient**. MüşteriHandler". İstek için, bu, diğer tüm işleyiciler çalıştırdıktan sonra ve istek gönderilmeden hemen önce oluşur. Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.

Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar. Bu, istek üstbilgisi veya yanıt durum kodu değişiklikleri içerebilir.

Günlük kategorisinde istemcinin adını niçin de dahil olmak üzere, belirli adlandırılmış istemciler için günlük filtreleme sağlar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler'ı yapılandırın

Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.

Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür. Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>iHttpClientFactory konsol uygulamasında kullanma

Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.
* `MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur `HttpClient`bir web sayfasını almak için kullanılır.
* `Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üstbilgi yayılımı ara ware

Üstbilgi yayılımı, gelen istekten giden HTTP İstemci isteklerine HTTP üstbilgilerini yayaymak için ASP.NET bir Çekirdek ara yazılımıdır. Üstbilgi yayılmasını kullanmak için:

* [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.
* Ara yazılımı yapılandırın ve `HttpClient` şu `Startup`şekilde:

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* İstemci, giden isteklerde yapılandırılan üstbilgiiçerir:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [JSON'u .NET'te serihale ve deserialize etme](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Glenn [Condron](https://github.com/glennc)tarafından , [Ryan Nowak](https://github.com/rynowak), ve [Steve Gordon](https://github.com/stevejgordon)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir. Aşağıdaki avantajları sunar:

* Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, bir *github* istemcisi kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir. Varsayılan istemci başka amaçlarla kaydedilebilir.
* Giden ara yazılım kavramını işleyicileri atayarak kodlar `HttpClient` ve Polly tabanlı ara yazılımiçin bundan yararlanmak için uzantılar sağlar.
* Yaşam ömürlerini el ile `HttpClientMessageHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Tüketim alışkanlıkları

Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden kesinlikle üstün değil. En iyi yaklaşım uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

Yöntemin `IHttpClientFactory` `AddHttpClient` `IServiceCollection`içinde, `Startup.ConfigureServices` uzantı yöntemi ni arayarak kaydedilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Bir kez kayıtlı, `IHttpClientFactory` kod bağımlılık [enjeksiyon (DI)](xref:fundamentals/dependency-injection)ile enjekte edilebilir her yerde hizmetleri kabul edebilirsiniz. Bir `IHttpClientFactory` `HttpClient` örnek oluşturmak için kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Bu `IHttpClientFactory` şekilde kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur. Bu şekilde `HttpClient` kullanılan hiçbir etkisi yoktur. Örneklerin `HttpClient` şu anda oluşturulduğu yerlerde, bu oluşumları bir çağrıyla değiştirin. <xref:System.Net.Http.IHttpClientFactory.CreateClient*>

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir `HttpClient`uygulama, her biri farklı bir yapılandırmaya sahip olan birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemcileri**kullanma seçeneği vardır. Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Önceki kodda, `AddHttpClient` adı *github*sağlayan denir. Bu istemci, temel&mdash;adres ve GitHub API ile çalışmak için gerekli iki üstbilgi uygulanan bazı varsayılan yapılandırma vardır.

Her `CreateClient` zaman çağrılır, `HttpClient` yeni bir örnek oluşturulur ve yapılandırma eylem denir.

Adlandırılmış bir istemciyi tüketmek için `CreateClient`dize parametresi . Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez. İstemci için yapılandırılan temel adres kullanıldığından, yalnızca yolu geçebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.
* Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.
* Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın. Örneğin, tek bir daktilo istemcisi tek bir arka uç bitiş noktası için kullanılabilir ve bu uç noktaile ilgili tüm mantığı kapsülleyebilir.
* DI ile çalışın ve uygulamanızda gerektiğinde enjekte edilebilir.

Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır. İşlevselliği ortaya `HttpClient` çıkaran API'ye özgü yöntemleri tanımlamak mümkündür. Yöntem, `GetAspNetDocsIssues` github deposundan en son açık sorunları sorgulamak ve ayrışdırmak için gereken kodu kapsüller.

Bir daktilan istemci kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> için, genel `Startup.ConfigureServices`uzantı yöntemi içinde kullanılabilir , daktilan istemci sınıfı belirterek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin oluşturucusu yerine, `Startup.ConfigureServices`kayıt sırasında yazılı istemcinin yapılandırması belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Yazılan istemciyi `HttpClient` tamamen kapsüllemek mümkündür. Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak adlandıran genel yöntemler sağlanabilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda, özel `HttpClient` alan olarak depolanır. Harici arama yapmak için tüm `GetRepos` erişim yöntemi geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıkları ile birlikte kullanılabilir. Refit ,NET için bir REST kitaplığıdır. REST API'lerini canlı arabirimlere dönüştürür. Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.

Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Giden istek middleware

`HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır. Bu, `IHttpClientFactory` her adlandırılmış istemci için uygulamak için işleyicileri tanımlamak kolaylaştırır. Giden bir istek ara yazılım boru hattı oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir. Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer. Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.

İşleyici oluşturmak için, 'den <xref:System.Net.Http.DelegatingHandler>kaynaklanan bir sınıf tanımlayın. İsteği `SendAsync` ardışık sistemdeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kıl:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Önceki kod temel bir işleyici tanımlar. `X-API-KEY` Üstbilginin isteğe ekilip eklenmediğini denetler. Üstbilgi eksikse, HTTP aramasını önleyebilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyicisi `HttpClient`için yapılandırmaya eklenebilir. Bu görev, uzantı yöntemleri <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>ile gerçekleştirilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır. Her `IHttpClientFactory` işleyici için ayrı bir DI kapsamı oluşturur. İşleyiciler herhangi bir kapsamdaki hizmetlere bağımlı olmakta serbesttir. İşleyicinin bağlı olduğu hizmetler, işleyici imha edildiğinde elden çıkarılır.

Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici için tür geçen, çağrılabilir.

Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak verileri işleyiciye `HttpRequestMessage.Properties`aktarın.
* Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.
* Verileri aktarmak `AsyncLocal` için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyicileri kullanma

`IHttpClientFactory`[Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kütüphanesi ile entegre . Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır. Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.

Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır. Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekleyin.
* [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işleme

En sık kullanılan hatalar, harici HTTP çağrıları geçici olduğunda oluşur. Geçici hataları işlemek `AddTransientHttpErrorPolicy` için bir ilke tanımlanmasına olanak sağlayan uygun bir uzatma yöntemi denir. Bu uzantı yöntemi ile `HttpRequestException`yapılandırılan ilkeler, HTTP 5xx yanıtları ve HTTP 408 yanıtları.

Uzantısı `AddTransientHttpErrorPolicy` içinde `Startup.ConfigureServices`kullanılabilir. Uzantı, olası `PolicyBuilder` geçici bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır. Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilkeleri seçin

Polly tabanlı işleyicileri eklemek için kullanılabilecek ek uzatma yöntemleri vardır. Böyle bir `AddPolicyHandler`uzantısı , birden fazla aşırı yükleri vardır. Bir aşırı yükleme, hangi politikanın uygulanacağı tanımlanırken isteğin denetlenmesini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır. Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyicisi ekleme

Gelişmiş işlevsellik sağlamak için Polly ilkelerini iç içe yerleştirmek yaygındır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Önceki örnekte, iki işleyicieklenir. İlk bir `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantısı kullanır. Başarısız istekler en fazla üç kez yeniden denendir. İkinci çağrı `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler. Beş başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir. Devre kesici ilkeleri durumludur. Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilkeler ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir . Kayıt defterinden bir ilke kullanılarak işleyicinin eklenmesine olanak tanıyan bir uzantı yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Önceki kodda, `PolicyRegistry` `ServiceCollection`iki ilke kaydedilir. Kayıt defterinden bir ilke `AddPolicyHandlerFromRegistry` kullanmak için, yöntem uygulanacak ilkenin adını geçerek kullanılır.

Polly `IHttpClientFactory` [wiki'de polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)hakkında daha fazla bilgi bulabilirsiniz.

## <a name="httpclient-and-lifetime-management"></a>Httpİste ve yaşam boyu yönetim

Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür. Her bir <xref:System.Net.Http.HttpMessageHandler> müşteri yeası var. `HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar. Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir. Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir. Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine tepki sini engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir. Geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemci `IHttpClientBuilder` oluştururken döndürülenleri arayın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin atılması gerekli değildir. Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder. `IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar. Örnekler `HttpClient` genellikle imha gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir. Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory için alternatifler

DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:

* Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.
* Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.

Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.

- Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.
- Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.

Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım soket yorgunluğunu önler.
- Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.

### <a name="cookies"></a>Tanımlama bilgileri

Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır. Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:

 - Otomatik çerez işlemeyi devre dışı bırakma
 - Kaçın -arak`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe kaydetme

Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.

Her istemci için kullanılan günlük kategorisi istemcinin adını içerir. *Örneğin MyNamedClient*adlı bir istemci, iletileri `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. *LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur. İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir. Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur. *MyNamedClient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir. İstek için, bu işlem, diğer tüm işleyiciler çalıştırdıktan sonra ve istek ağa gönderilmeden hemen önce oluşur. Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.

Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar. Bu, örneğin istek üstbilgisi veya yanıt durum kodunda yapılan değişiklikleri içerebilir.

Günlük kategorisine istemcinin adını niçin eklenmesi gerektiğinde belirli adlandırılmış istemciler için günlük filtreleme sağlar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler'ı yapılandırın

Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.

Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür. Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>iHttpClientFactory konsol uygulamasında kullanma

Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.
* `MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur `HttpClient`bir web sayfasını almak için kullanılır.
* `Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Glenn [Condron](https://github.com/glennc)tarafından , [Ryan Nowak](https://github.com/rynowak), ve [Steve Gordon](https://github.com/stevejgordon)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve <xref:System.Net.Http.HttpClient> oluşturmak için kaydedilebilir ve kullanılabilir. Aşağıdaki avantajları sunar:

* Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, bir *github* istemcisi kaydedilebilir ve [GitHub'a](https://github.com/)erişmek için yapılandırılabilir. Varsayılan istemci başka amaçlarla kaydedilebilir.
* Giden ara yazılım kavramını işleyicileri atayarak kodlar `HttpClient` ve Polly tabanlı ara yazılımiçin bundan yararlanmak için uzantılar sağlar.
* Yaşam ömürlerini el ile `HttpClientMessageHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

.NET Framework'ü hedefleyen projeler [microsoft.extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir. .NET Core'u hedefleyen ve [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app) başvuran projeler zaten `Microsoft.Extensions.Http` paketi içerir.

## <a name="consumption-patterns"></a>Tüketim alışkanlıkları

Bir uygulamada `IHttpClientFactory` kullanılabilen çeşitli yollar vardır:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden kesinlikle üstün değil. En iyi yaklaşım uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

Yöntemin `IHttpClientFactory` `AddHttpClient` `IServiceCollection`içinde, `Startup.ConfigureServices` uzantı yöntemi ni arayarak kaydedilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Bir kez kayıtlı, `IHttpClientFactory` kod bağımlılık [enjeksiyon (DI)](xref:fundamentals/dependency-injection)ile enjekte edilebilir her yerde hizmetleri kabul edebilirsiniz. Bir `IHttpClientFactory` `HttpClient` örnek oluşturmak için kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Bu `IHttpClientFactory` şekilde kullanmak, varolan bir uygulamayı yeniden düzenlemenin iyi bir yoludur. Bu şekilde `HttpClient` kullanılan hiçbir etkisi yoktur. Örneklerin `HttpClient` şu anda oluşturulduğu yerlerde, bu oluşumları bir çağrıyla değiştirin. <xref:System.Net.Http.IHttpClientFactory.CreateClient*>

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir `HttpClient`uygulama, her biri farklı bir yapılandırmaya sahip olan birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemcileri**kullanma seçeneği vardır. Bir adlandırılmış `HttpClient` için yapılandırma kayıt `Startup.ConfigureServices`sırasında belirtilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Önceki kodda, `AddHttpClient` adı *github*sağlayan denir. Bu istemci, temel&mdash;adres ve GitHub API ile çalışmak için gerekli iki üstbilgi uygulanan bazı varsayılan yapılandırma vardır.

Her `CreateClient` zaman çağrılır, `HttpClient` yeni bir örnek oluşturulur ve yapılandırma eylem denir.

Adlandırılmış bir istemciyi tüketmek için `CreateClient`dize parametresi . Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Önceki kodda, istek bir ana bilgisayar adı belirtmek gerekmez. İstemci için yapılandırılan temel adres kullanıldığından, yalnızca yolu geçebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanmaya gerek kalmadan adlandırılmış istemcilerle aynı yetenekleri sağlayın.
* Müşterileri tüketirken IntelliSense ve derleyici yardımı sağlar.
* Belirli bir yeri yapılandırmak ve etkileşimde bulunabilmek `HttpClient`için tek bir konum sağlayın. Örneğin, tek bir daktilo istemcisi tek bir arka uç bitiş noktası için kullanılabilir ve bu uç noktaile ilgili tüm mantığı kapsülleyebilir.
* DI ile çalışın ve uygulamanızda gerektiğinde enjekte edilebilir.

Yazılan istemci, oluşturucusu bir `HttpClient` parametre kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. Nesne `HttpClient` bir kamu malı olarak ortaya çıkarır. İşlevselliği ortaya `HttpClient` çıkaran API'ye özgü yöntemleri tanımlamak mümkündür. Yöntem, `GetAspNetDocsIssues` github deposundan en son açık sorunları sorgulamak ve ayrışdırmak için gereken kodu kapsüller.

Bir daktilan istemci kaydetmek <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> için, genel `Startup.ConfigureServices`uzantı yöntemi içinde kullanılabilir , daktilan istemci sınıfı belirterek:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan enjekte edilebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin oluşturucusu yerine, `Startup.ConfigureServices`kayıt sırasında yazılı istemcinin yapılandırması belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Yazılan istemciyi `HttpClient` tamamen kapsüllemek mümkündür. Bir özellik olarak teşhir etmek yerine, `HttpClient` örneği dahili olarak adlandıran genel yöntemler sağlanabilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda, özel `HttpClient` alan olarak depolanır. Harici arama yapmak için tüm `GetRepos` erişim yöntemi geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`[Refit](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıkları ile birlikte kullanılabilir. Refit ,NET için bir REST kitaplığıdır. REST API'lerini canlı arabirimlere dönüştürür. Arabirimin bir uygulaması, harici HTTP `RestService`aramaları `HttpClient` yapmak için kullanılarak dinamik olarak oluşturulur.

Dış API'yi ve yanıtını temsil edecek bir arabirim ve yanıt tanımlanır:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Uygulamayı oluşturmak için Refit kullanılarak yazılan bir istemci eklenebilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Tanımlanan arabirim, DI ve Refit tarafından sağlanan uygulama ile gerektiğinde tüketilebilir:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Giden istek middleware

`HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır. Bu, `IHttpClientFactory` her adlandırılmış istemci için uygulamak için işleyicileri tanımlamak kolaylaştırır. Giden bir istek ara yazılım boru hattı oluşturmak için birden çok işleyicinin kaydedilmesi ve zincirlemesi destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş yapabilir. Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer. Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.

İşleyici oluşturmak için, 'den <xref:System.Net.Http.DelegatingHandler>kaynaklanan bir sınıf tanımlayın. İsteği `SendAsync` ardışık sistemdeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kıl:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Önceki kod temel bir işleyici tanımlar. `X-API-KEY` Üstbilginin isteğe ekilip eklenmediğini denetler. Üstbilgi eksikse, HTTP aramasını önleyebilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyicisi `HttpClient`için yapılandırmaya eklenebilir. Bu görev, uzantı yöntemleri <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>ile gerçekleştirilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Önceki kodda, DI `ValidateHeaderHandler` kayıtlıdır. İşleyici, di'ye geçici bir hizmet olarak **kaydedilmeli,** hiçbir zaman kapsama alınmalıdır. İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağlı olduğu hizmetler tek kullanımlıksa:

* Işleyicinin hizmetleri kapsam dışına çıkmadan önce elden çıkarılabilir.
* Elden çıkarılan işleyici hizmetleri işleyicinin başarısız olması için neden olur.

Bir kez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> kaydedildikten sonra, işleyici türünde geçerek çağrılabilir.

Birden çok işleyici, yürütmeleri gerektiği sırada kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İsteme başına durumu ileti işleyicileriyle paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak verileri işleyiciye `HttpRequestMessage.Properties`aktarın.
* Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.
* Verileri aktarmak `AsyncLocal` için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyicileri kullanma

`IHttpClientFactory`[Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kütüphanesi ile entegre . Polly, .NET için kapsamlı bir esneklik ve geçici hata işleme kitaplığıdır. Geliştiricilerin Retry, Circuit Breaker, Timeout, Bulkhead Isolation ve Fallback gibi ilkeleri akıcı ve iş parçacığı güvenli bir şekilde ifade etmesine olanak tanır.

Polly ilkelerinin yapılandırılmış `HttpClient` örneklerle kullanımını etkinleştirmek için uzantı yöntemleri sağlanır. Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekleyin.
* [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işleme

En sık kullanılan hatalar, harici HTTP çağrıları geçici olduğunda oluşur. Geçici hataları işlemek `AddTransientHttpErrorPolicy` için bir ilke tanımlanmasına olanak sağlayan uygun bir uzatma yöntemi denir. Bu uzantı yöntemi ile `HttpRequestException`yapılandırılan ilkeler, HTTP 5xx yanıtları ve HTTP 408 yanıtları.

Uzantısı `AddTransientHttpErrorPolicy` içinde `Startup.ConfigureServices`kullanılabilir. Uzantı, olası `PolicyBuilder` geçici bir hatayı temsil eden hataları işlemek için yapılandırılan bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Önceki kodda bir `WaitAndRetryAsync` ilke tanımlanır. Başarısız istekler, denemeler arasında 600 ms'lik bir gecikmeyle üç defaya kadar yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilkeleri seçin

Polly tabanlı işleyicileri eklemek için kullanılabilecek ek uzatma yöntemleri vardır. Böyle bir `AddPolicyHandler`uzantısı , birden fazla aşırı yükleri vardır. Bir aşırı yükleme, hangi politikanın uygulanacağı tanımlanırken isteğin denetlenmesini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Önceki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman ayarı uygulanır. Başka bir HTTP yöntemi için 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyicisi ekleme

Gelişmiş işlevsellik sağlamak için Polly ilkelerini iç içe yerleştirmek yaygındır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Önceki örnekte, iki işleyicieklenir. İlk bir `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantısı kullanır. Başarısız istekler en fazla üç kez yeniden denendir. İkinci çağrı `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler. Beş başarısız deneme sırayla gerçekleşirse, diğer dış istekler 30 saniye boyunca engellenir. Devre kesici ilkeleri durumludur. Bu istemci den gelen tüm aramalar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilkeler ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları `PolicyRegistry`bir kez tanımlamak ve bir . Kayıt defterinden bir ilke kullanılarak işleyicinin eklenmesine olanak tanıyan bir uzantı yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Önceki kodda, `PolicyRegistry` `ServiceCollection`iki ilke kaydedilir. Kayıt defterinden bir ilke `AddPolicyHandlerFromRegistry` kullanmak için, yöntem uygulanacak ilkenin adını geçerek kullanılır.

Polly `IHttpClientFactory` [wiki'de polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)hakkında daha fazla bilgi bulabilirsiniz.

## <a name="httpclient-and-lifetime-management"></a>Httpİste ve yaşam boyu yönetim

Yeni `HttpClient` bir örnek her `CreateClient` zaman `IHttpClientFactory`döndürülür. Her bir <xref:System.Net.Http.HttpMessageHandler> müşteri yeası var. `HttpMessageHandler` Fabrika, örneklerin yaşam ömürlerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri bir araya sağlar. Bir `HttpMessageHandler` örnek, kullanım ömrü dolmamışsa `HttpClient` yeni bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönetir gibi işleyicileri havuzlama arzu edilir. Gerekenden daha fazla işleyici oluşturmak bağlantı gecikmelerine neden olabilir. Bazı işleyiciler de bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine tepki sini engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci bazında geçersiz kılınabilir. Geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemci `IHttpClientBuilder` oluştururken döndürülenleri arayın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin atılması gerekli değildir. Elden çıkarma giden istekleri iptal eder `HttpClient` ve verilen örneğin aramadan <xref:System.IDisposable.Dispose*>sonra kullanılamayacağından garanti eder. `IHttpClientFactory`örnekleri tarafından `HttpClient` kullanılan kaynakları izler ve elden çıkar. Örnekler `HttpClient` genellikle imha gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneği uzun süre canlı tutmak, kuruluşundan önce `IHttpClientFactory`kullanılan yaygın bir desendir. Bu desen' e geçtikten `IHttpClientFactory`sonra gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>IHttpClientFactory için alternatifler

DI `IHttpClientFactory` özellikli bir uygulamada kullanmak şunları önler:

* Örnekleri birleştirerek `HttpMessageHandler` kaynak tükenmesi sorunları.
* Düzenli aralıklarla bisiklet `HttpMessageHandler` örnekleri tarafından Bayat DNS sorunları.

Uzun ömürlü bir örneği kullanarak önceki sorunları çözmek <xref:System.Net.Http.SocketsHttpHandler> için alternatif yollar vardır.

- Uygulamanın ne `SocketsHttpHandler` zaman başladığına bir örnek oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS yenileme sürelerine göre uygun bir değere göre <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yapılandırın.
- Gerektiğinde `HttpClient` kullanarak `new HttpClient(handler, disposeHandler: false)` örnekler oluşturun.

Önceki yaklaşımlar benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- Örnekler `SocketsHttpHandler` arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım soket yorgunluğunu önler.
- Eski `SocketsHttpHandler` DNS sorunlarını `PooledConnectionLifetime` önlemek için döngüleri bağlantıları.

### <a name="cookies"></a>Tanımlama bilgileri

Birleştirilmiş `HttpMessageHandler` örnekler, nesnelerin `CookieContainer` paylaşılmasına neden oluyor. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle yanlış kodla sonuçlanır. Tanımlama bilgisi gerektiren uygulamalar için aşağıdakileri göz önünde bulundurun:

 - Otomatik çerez işlemeyi devre dışı bırakma
 - Kaçın -arak`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> çerez kullanımını devre dışı kılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe kaydetme

Tüm istekler için kayıt günlüğü iletileri aracılığıyla `IHttpClientFactory` oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üstbilgilerinin günlüğe kaydedilmesi gibi ek günlüğe kaydetme yalnızca izleme düzeyinde dahildir.

Her istemci için kullanılan günlük kategorisi istemcinin adını içerir. *Örneğin MyNamedClient*adlı bir istemci, iletileri `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. *LogicalHandler* ile sabitlenmiş iletiler istek işleyicisi ardışık alanıdışında oluşur. İstek üzerine, iletiler ardışık ardışık işlemden önce günlüğe kaydedilir. Yanıtta, iletiler yanıtı aldıktan sonra günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık hattının içinde de oluşur. *MyNamedClient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir. İstek için, bu işlem, diğer tüm işleyiciler çalıştırdıktan sonra ve istek ağa gönderilmeden hemen önce oluşur. Yanıtta, bu günlüğe kaydetme, işleyici ardışık işlemden geçmeden önce yanıtın durumunu içerir.

Boru hattının dışında ve içinde günlüğe kaydetmeyi etkinleştirmek, diğer boru hattı işleyicileri tarafından yapılan değişikliklerin incelenmesini sağlar. Bu, örneğin istek üstbilgisi veya yanıt durum kodunda yapılan değişiklikleri içerebilir.

Günlük kategorisine istemcinin adını niçin eklenmesi gerektiğinde belirli adlandırılmış istemciler için günlük filtreleme sağlar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler'ı yapılandırın

Bir istemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmasını denetlemek için gerekli olabilir.

Adlandırılmış veya yazılan istemciler eklendiğinde bir `IHttpClientBuilder` döndürülür. Uzantı <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> yöntemi bir temsilci tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan `HttpMessageHandler` birincil oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>iHttpClientFactory konsol uygulamasında kullanma

Konsol uygulamasında, projeye aşağıdaki paket başvurularını ekleyin:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>Genel Ana [Bilgisayar hizmet](xref:fundamentals/host/generic-host) konteynerine kaydedilir.
* `MyService`bir `HttpClient`. oluşturmak için kullanılan hizmetten bir istemci fabrika örneği oluşturur `HttpClient`bir web sayfasını almak için kullanılır.
* `Main`hizmetin `GetPage` yöntemini yürütmek ve web sayfası içeriğinin ilk 500 karakterini konsola yazmak için bir kapsam oluşturur.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üstbilgi yayılımı ara ware

Üstbilgi yayılımı, gelen istekten giden HTTP İstemci isteklerine HTTP üstbilgilerini yaymak için desteklenen bir topluluk orta yazılımıdır. Üstbilgi yayılmasını kullanmak için:

* Başvuru topluluk paketinin bağlantı noktası desteklenen [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation). ASP.NET Core 3.1 ve daha sonra [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.

* Ara yazılımı yapılandırın ve `HttpClient` şu `Startup`şekilde:

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* İstemci, giden isteklerde yapılandırılan üstbilgiiçerir:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly politikaları ile üstel geri dönüş ile HTTP çağrı yeniden uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
