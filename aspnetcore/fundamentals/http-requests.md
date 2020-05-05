---
title: ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın
author: stevejgordon
description: ASP.NET Core içindeki mantıksal HttpClient örneklerini yönetmek için ıhttpclientfactory arabirimini kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766556"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın

::: moniker range=">= aspnetcore-3.0"

[Glenn CONDRON](https://github.com/glennc), [Ryan şimdi ak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Kirk larkabağı](https://github.com/serpent5)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir. `IHttpClientFactory`aşağıdaki avantajları sunar:

* , Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *GitHub* adlı bir Istemci, [GitHub](https://github.com/)'a erişmek için kaydedilebilir ve yapılandırılabilir. Varsayılan istemci, genel erişim için kaydedilebilir.
* ' De `HttpClient`işleyiciler temsilci seçme yoluyla giden ara yazılım kavramını daha da artırır. ' De `HttpClient`işleyiciler temsilci atama avantajlarından faydalanmak Için, Polya tabanlı bir ara yazılım için uzantılar sağlar.
* Temel `HttpClientMessageHandler` örneklerin biriktirmesini ve ömrünü yönetir. Otomatik yönetim, yaşam sürelerini el ile yönetirken `HttpClient` oluşan ortak DNS (etki alanı adı sistemi) sorunlarını önler.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu konu sürümündeki örnek kod, HTTP yanıtlarında döndürülen JSON içeriğinin serisini kaldırmak için kullanır <xref:System.Text.Json> . Ve `Json.NET` `ReadAsAsync<T>`kullanan örnekler için, bu konunun 2. x sürümünü seçmek üzere sürüm seçiciyi kullanın.

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

En iyi yaklaşım, uygulamanın gereksinimlerine bağlı olarak değişir.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory`, çağırarak `AddHttpClient`kaydedilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

`IHttpClientFactory` [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanılarak bir istek yapılabilir. Aşağıdaki kod bir `HttpClient` örnek `IHttpClientFactory` oluşturmak için kullanır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Yukarıdaki `IHttpClientFactory` örnekte olduğu gibi kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım hakkında hiçbir etkisi `HttpClient` yoktur. Var olan bir `HttpClient` uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumların ' i çağrılarıyla değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Adlandırılmış istemciler

Adlandırılmış istemciler şu durumlarda iyi bir seçimdir:

* Uygulama birçok farklı kullanımı gerektirir `HttpClient`.
* Birçok `HttpClient`s farklı yapılandırmaya sahiptir.

Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

İstemcinin yapılandırıldığı önceki kodda:

* Temel adres `https://api.github.com/`.
* GitHub API 'SI ile çalışmak için iki üst bilgi gereklidir.

#### <a name="createclient"></a>CreateClient

Her zaman <xref:System.Net.Http.IHttpClientFactory.CreateClient*> çağrılır:

* Yeni bir örneği `HttpClient` oluşturulur.
* Yapılandırma eylemi çağrılır.

Adlandırılmış bir istemci oluşturmak için adını içine `CreateClient`geçirin:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, kod yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin. Örneğin, tek bir türü belirtilmiş istemci kullanılabilir:
  * Tek bir arka uç uç noktası için.
  * Uç nokta ile ilgili tüm mantığı kapsüllemek için.
* DI ile birlikte çalışın ve uygulamada gerektiğinde eklenebilir.

Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Yukarıdaki kodda:

* Yapılandırma, yazılan istemciye taşınır.
* `HttpClient` Nesne bir ortak özellik olarak sunulur.

İşlevselliği kullanıma `HttpClient` sunan, API 'ye özgü Yöntemler oluşturulabilir. Örneğin, `GetAspNetDocsIssues` yöntemi açık sorunları almak için kodu kapsüller.

Aşağıdaki kod, türü <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> belirtilmiş `Startup.ConfigureServices` bir istemci sınıfını kaydetmek için ' de çağırır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yukarıdaki kodda geçici bir hizmet `AddHttpClient` olarak `GitHubService` kaydedilir. Bu kayıt, için bir fabrika yöntemi kullanır:

1. `HttpClient` örneği oluşturun.
1. Örneğini oluşturucusuna geçirerek `HttpClient` bir `GitHubService`örneğini oluşturun.

Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Türü belirtilmiş bir istemcinin yapılandırması `Startup.ConfigureServices`, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

, `HttpClient` Türü belirlenmiş bir istemci içinde kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran bir yöntem tanımlayın:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Yukarıdaki kodda `HttpClient` , bir özel alanda depolanır. Öğesine erişimi, `HttpClient` genel `GetRepos` yöntemi tarafından yapılır.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıklarla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.

Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:

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

Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:

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

Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.

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

## <a name="outgoing-request-middleware"></a>Giden istek ara yazılımı

`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramıdır. `IHttpClientFactory`:

* Her bir adlandırılmış istemci için uygulanacak işleyiciler tanımlamayı basitleştirir.
* Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydedilmesini ve zincirleme kullanımını destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir. Bu model:

  * ASP.NET Core gelen ara yazılım ardışık düzenine benzerdir.
  * , HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar, örneğin:

    * önbelleği
    * hata işleme
    * getir
    * günlüğe kaydetme

Temsilci seçme işleyicisi oluşturmak için:

* Türet <xref:System.Net.Http.DelegatingHandler>.
* Geçersiz <xref:System.Net.Http.DelegatingHandler.SendAsync*>kıl. İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütün:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, `X-API-KEY` üstbilginin istekte olup olmadığını denetler. `X-API-KEY` Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.

İçin `HttpClient` yapılandırmasına birden fazla işleyici eklenebilir <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir. Her `IHttpClientFactory` işleyici için ayrı bir dı kapsamı oluşturur. İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olabilir. İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak işleyicide veri geçirin.
* Geçerli <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> isteğe erişmek için kullanın.
* Verileri geçirmek için <xref:System.Threading.AsyncLocal`1> özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`üçüncü taraf kitaplığı [Polly](https://github.com/App-vNext/Polly)ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır. Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler. Polly, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini gerektirir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Hatalar genellikle dış HTTP çağrıları geçici olduğunda oluşur. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>geçici hataları işlemek için bir ilkenin tanımlanmasını sağlar. Aşağıdaki yanıtları `AddTransientHttpErrorPolicy` işleyecek şekilde yapılandırılan ilkeler:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy`olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Uzantı yöntemleri, örneğin, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>Polly tabanlı işleyiciler eklemek için sağlanır. Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi ilkenin uygulanacağını belirlemek için isteği inceler:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Polly ilkeleri iç içe almak yaygın bir şekilde yapılır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte:

* İki işleyici eklenir.
* İlk işleyici, yeniden <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> deneme ilkesi eklemek için kullanır. Başarısız istekler en fazla üç kez yeniden denenir.
* İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler. 5 başarısız girişim sıralı olarak gerçekleşirse, daha fazla dış istek 30 saniye boyunca engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir.

Aşağıdaki kodda:

* "Normal" ve "uzun" ilkeler eklenmiştir.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>kayıt defterinden "normal" ve "uzun" ilkeleri ekler.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Ve daha fazla tümleştirme `IHttpClientFactory` hakkında daha fazla bilgi için bkz. [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`. <xref:System.Net.Http.HttpMessageHandler> Adlandırılmış istemci başına oluşturulur. Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (etki alanı adı sistemi) değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`örnekler genellikle aktiften **çıkarma gerektirmeyen .NET nesneleri olarak** kabul edilebilir. Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar. `IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık.

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir. Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:

* Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.
* Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.

Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.

- Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.
- Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.

Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım, yuva azalmasına engel olur.
- Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmasında uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, "System .net. http. HttpClient" kategorisine sahip iletileri günlüğe kaydeder. **Mynamedclient**. LogicalHandler ". *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu Iletiler "System .net. http. HttpClient" günlük kategorisiyle günlüğe kaydedilir. **Mynamedclient**. ClientHandler ". İstek için bu, tüm diğer işleyiciler çalıştırıldıktan sonra ve istek gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, istek üst bilgilerinde veya yanıt durum kodunda yapılan değişiklikleri içerebilir.

İstemcinin adını log kategorisinde da içermek, belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.

Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur. `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üst bilgi yayma ara yazılımı

Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymaya yönelik bir ASP.NET Core ara istemcindedir. Üst bilgi yaymayı kullanmak için:

* [Microsoft. AspNetCore. Headeryayma](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.
* Ara yazılımı ve `HttpClient` içinde `Startup`yapılandırın:

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* İstemci giden isteklerde yapılandırılan üst bilgileri içerir:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [.NET 'te JSON serileştirme ve serisini kaldırma](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir. Aşağıdaki avantajları sunar:

* , Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir. Varsayılan istemci, diğer amaçlar için kaydedilebilir.
* ' De `HttpClient` işleyiciler için temsilci atama ile giden ara yazılım kavramı ve bundan faydalanmak Için, Polly tabanlı ara yazılım için uzantılar sağlar.
* Yaşam sürelerini el ile yönetirken `HttpClientMessageHandler` `HttpClient` gerçekleşen yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden tamamen üst değildir. En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory` `AddHttpClient` , `Startup.ConfigureServices` Yöntemi içindeki içindeki genişletme yöntemi `IServiceCollection`çağırarak kaydedilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir. `IHttpClientFactory` Bir `HttpClient` örnek oluşturmak için kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Bu `IHttpClientFactory` biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım şekli `HttpClient` üzerinde hiçbir etkisi yoktur. `HttpClient` Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir uygulama `HttpClient`, her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır. Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir. Bu istemci, GitHub API 'siyle birlikte&mdash;çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.

Her seferinde `CreateClient` her çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.

Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient`. Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin. Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.
* DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.

Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. `HttpClient` Nesne bir ortak özellik olarak sunulur. İşlevselliği kullanıma `HttpClient` sunan API 'ye özel yöntemler tanımlamak mümkündür. Yöntemi `GetAspNetDocsIssues` , GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.

Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde `Startup.ConfigureServices`kullanılabilir istemci sınıfını belirterek kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin yapılandırması, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt `Startup.ConfigureServices`sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Türü belirtilmiş bir istemci `HttpClient` içinde tamamen kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran ortak Yöntemler sunulabilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda `HttpClient` , bir özel alan olarak depolanır. Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.

Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:

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

Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:

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

Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.

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

## <a name="outgoing-request-middleware"></a>Giden istek ara yazılımı

`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var. , `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır. Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir. Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer. Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.

Bir işleyici oluşturmak için, öğesinden türeten <xref:System.Net.Http.DelegatingHandler>bir sınıf tanımlayın. İsteği ardışık `SendAsync` düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, temel bir işleyiciyi tanımlar. İsteğe bağlı bir `X-API-KEY` başlık olup olmadığını denetler. Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyici bir `HttpClient`için yapılandırmasına eklenebilir. Bu görev, <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir. Her `IHttpClientFactory` işleyici için ayrı bir dı kapsamı oluşturur. İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olarak ücretsizdir. İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak `HttpRequestMessage.Properties`işleyicide veri geçirin.
* Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.
* Verileri geçirmek için `AsyncLocal` özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır. Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.
* , [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur. Geçici hataları işlemek üzere bir `AddTransientHttpErrorPolicy` ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir. Bu uzantı yöntemi tanıtıcısıyla `HttpRequestException`yapılandırılmış ilkeler, http 5xx YANıTLARı ve http 408 yanıtları.

`AddTransientHttpErrorPolicy` Uzantı içinde `Startup.ConfigureServices`kullanılabilir. Uzantı, olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır. Bu tür bir uzantının `AddPolicyHandler`birden çok aşırı yüklemesi vardır. Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte, iki işleyici eklenmiştir. İlki, yeniden deneme `AddTransientHttpErrorPolicy` ilkesi eklemek için uzantıyı kullanır. Başarısız istekler en fazla üç kez yeniden denenir. İçin `AddTransientHttpErrorPolicy` ikinci çağrı, bir devre kesici ilkesi ekler. Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir. Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection`. Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.

Ve daha fazla `IHttpClientFactory` tümleştirme hakkında daha fazla bilgi, [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`. Adlandırılmış istemci <xref:System.Net.Http.HttpMessageHandler> başına. Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir. Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemcisini oluştururken `IHttpClientBuilder` döndürülen öğesini çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin çıkarılması gerekli değildir. Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar. `IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık. `HttpClient` Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir. Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:

* Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.
* Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.

Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.

- Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.
- Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.

Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım, yuva azalmasına engel olur.
- Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir. İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.

İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.

Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur. `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak <xref:System.Net.Http.HttpClient> için kayıt yapılabilir ve kullanılabilir. Aşağıdaki avantajları sunar:

* , Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir. Varsayılan istemci, diğer amaçlar için kaydedilebilir.
* ' De `HttpClient` işleyiciler için temsilci atama ile giden ara yazılım kavramı ve bundan faydalanmak Için, Polly tabanlı ara yazılım için uzantılar sağlar.
* Yaşam sürelerini el ile yönetirken `HttpClientMessageHandler` `HttpClient` gerçekleşen yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

.NET Framework hedefleyen projeler [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir. .NET Core ile hedeflenen ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvuran projeler zaten `Microsoft.Extensions.Http` paketi içeriyor.

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden tamamen üst değildir. En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory` `AddHttpClient` , `Startup.ConfigureServices` Yöntemi içindeki içindeki genişletme yöntemi `IServiceCollection`çağırarak kaydedilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir. `IHttpClientFactory` Bir `HttpClient` örnek oluşturmak için kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

Bu `IHttpClientFactory` biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım şekli `HttpClient` üzerinde hiçbir etkisi yoktur. `HttpClient` Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir uygulama `HttpClient`, her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır. Adlandırılmış `HttpClient` için yapılandırma, içinde `Startup.ConfigureServices`kayıt sırasında belirtilebilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir. Bu istemci, GitHub API 'siyle birlikte&mdash;çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.

Her seferinde `CreateClient` her çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.

Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient`. Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli `HttpClient`bir ile etkileşimde bulunmak için tek bir konum belirtin. Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.
* DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.

Türü belirtilmiş istemci, oluşturucusunda `HttpClient` bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. `HttpClient` Nesne bir ortak özellik olarak sunulur. İşlevselliği kullanıma `HttpClient` sunan API 'ye özel yöntemler tanımlamak mümkündür. Yöntemi `GetAspNetDocsIssues` , GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.

Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde `Startup.ConfigureServices`kullanılabilir istemci sınıfını belirterek kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin yapılandırması, türü belirlenmiş istemcinin Oluşturucusu yerine kayıt `Startup.ConfigureServices`sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Türü belirtilmiş bir istemci `HttpClient` içinde tamamen kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, `HttpClient` örneği dahili olarak çağıran ortak Yöntemler sunulabilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda `HttpClient` , bir özel alan olarak depolanır. Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, dış HTTP çağrıları yapmak için kullanılarak `RestService` `HttpClient` tarafından dinamik olarak oluşturulur.

Bir arabirim ve yanıt, dış API 'yi ve yanıtını temsil edecek şekilde tanımlanır:

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

Türü belirlenmiş bir istemci eklenebilir, uygulamayı oluşturmak için yeniden sığdırma kullanımı kullanılabilir:

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

Tanımlı arabirim, gereken yerde, mak ve Refit tarafından sağlanmış uygulama ile kullanılabilir.

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

## <a name="outgoing-request-middleware"></a>Giden istek ara yazılımı

`HttpClient`, giden HTTP istekleri için birlikte bağlanabilen işleyicileri temsilci seçme kavramı zaten var. , `IHttpClientFactory` Her bir adlandırılmış istemci için uygulanacak işleyicileri tanımlamanızı kolaylaştırır. Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok işleyicinin kaydını ve zincirlemeyi destekler. Bu işleyicilerin her biri, giden istekten önce ve sonra iş gerçekleştirebilir. Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer. Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.

Bir işleyici oluşturmak için, öğesinden türeten <xref:System.Net.Http.DelegatingHandler>bir sınıf tanımlayın. İsteği ardışık `SendAsync` düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, temel bir işleyiciyi tanımlar. İsteğe bağlı bir `X-API-KEY` başlık olup olmadığını denetler. Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyici bir `HttpClient`için yapılandırmasına eklenebilir. Bu görev, <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Yukarıdaki kodda `ValidateHeaderHandler` ,, dı ile kaydedilir. İşleyicinin, bir geçici hizmet olarak dı 'ye kayıtlı olması **gerekir** , hiçbir koşulda kapsamı yoktur. İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağımlı olduğu tüm hizmetler atılabilir olur:

* İşleyici kapsam dışına geçmeden önce işleyicinin Hizmetleri atılamaz.
* Atılmış işleyici Hizmetleri işleyicinin başarısız olmasına neden olur.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyici türünü geçirerek çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son `HttpClientHandler` isteği çalıştırana kadar sonraki işleyiciyi sarmalar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak `HttpRequestMessage.Properties`işleyicide veri geçirin.
* Geçerli `IHttpContextAccessor` isteğe erişmek için kullanın.
* Verileri geçirmek için `AsyncLocal` özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış `HttpClient` örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır. Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.
* , [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur. Geçici hataları işlemek üzere bir `AddTransientHttpErrorPolicy` ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir. Bu uzantı yöntemi tanıtıcısıyla `HttpRequestException`yapılandırılmış ilkeler, http 5xx YANıTLARı ve http 408 yanıtları.

`AddTransientHttpErrorPolicy` Uzantı içinde `Startup.ConfigureServices`kullanılabilir. Uzantı, olası bir geçici hatayı `PolicyBuilder` temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır. Bu tür bir uzantının `AddPolicyHandler`birden çok aşırı yüklemesi vardır. Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte, iki işleyici eklenmiştir. İlki, yeniden deneme `AddTransientHttpErrorPolicy` ilkesi eklemek için uzantıyı kullanır. Başarısız istekler en fazla üç kez yeniden denenir. İçin `AddTransientHttpErrorPolicy` ikinci çağrı, bir devre kesici ilkesi ekler. Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir `PolicyRegistry`ile kaydetmektir. Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection`. Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.

Ve daha fazla `IHttpClientFactory` tümleştirme hakkında daha fazla bilgi, [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

Her `HttpClient` `CreateClient` çağrıldığında yeni bir örnek döndürülür `IHttpClientFactory`. Adlandırılmış istemci <xref:System.Net.Http.HttpMessageHandler> başına. Fabrika, `HttpMessageHandler` örneklerin yaşam sürelerini yönetir.

`IHttpClientFactory`kaynak tüketimini `HttpMessageHandler` azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni `HttpClient` bir örnek oluştururken havuzdan yeniden kullanılabilir.

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir. Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> istemcisini oluştururken `IHttpClientBuilder` döndürülen öğesini çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin çıkarılması gerekli değildir. Çıkarma giden istekleri iptal eder ve çağırma `HttpClient` <xref:System.IDisposable.Dispose*>sonrasında verilen örneğin kullanılamaz olmasını sağlar. `IHttpClientFactory`örnekler tarafından `HttpClient` kullanılan kaynakları izler ve ortadan kaldırdık. `HttpClient` Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde `IHttpClientFactory`kullanılmadan önce kullanılan ortak bir modeldir. Bu model, ' a geçtikten sonra `IHttpClientFactory`gereksiz hale gelir.

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

Dı `IHttpClientFactory` etkin bir uygulamada kullanmak şunları önler:

* Havuz `HttpMessageHandler` örneklerine göre kaynak tükenmesi sorunları.
* Düzenli aralıklarla `HttpMessageHandler` örnekleri GEÇIREREK eski DNS sorunları.

Uzun süreli <xref:System.Net.Http.SocketsHttpHandler> bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır.

- Uygulamanın başladığı `SocketsHttpHandler` zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- DNS <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> yenileme süreleri temelinde uygun bir değere yapılandırın.
- Gerektiğinde `HttpClient` örnek `new HttpClient(handler, disposeHandler: false)` oluşturun.

Yukarıdaki yaklaşımlar, benzer bir şekilde `IHttpClientFactory` çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında `HttpClient` bağlantıları paylaşır. Bu paylaşım, yuva azalmasına engel olur.
- Bağlantıları `SocketsHttpHandler` , eski DNS sorunlarından `PooledConnectionLifetime` kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, paylaşılan `CookieContainer` nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

Otomatik <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

Tüm istekler için `IHttpClientFactory` kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu iletiler günlük kategorisine `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`göre günlüğe kaydedilir. İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.

İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç `HttpMessageHandler` yapılandırmayı denetlemek gerekli olabilir.

Adlandırılmış `IHttpClientBuilder` veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, bu istemci tarafından kullanılan birincili `HttpMessageHandler` oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir `HttpClient`istemci fabrikası örneği oluşturur. `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`Hizmetin `GetPage` yöntemini yürütmek için bir kapsam oluşturur ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üst bilgi yayma ara yazılımı

Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymak için bir topluluk tarafından desteklenen bir ara yazılımlar. Üst bilgi yaymayı kullanmak için:

* Topluluğun, paket [Headeryayılmasının](https://www.nuget.org/packages/HeaderPropagation)desteklediği bağlantı noktasına başvurun. ASP.NET Core 3,1 ve üzeri, [Microsoft. AspNetCore. Headeryayılmayı](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.

* Ara yazılımı ve `HttpClient` içinde `Startup`yapılandırın:

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* İstemci giden isteklerde yapılandırılan üst bilgileri içerir:

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
