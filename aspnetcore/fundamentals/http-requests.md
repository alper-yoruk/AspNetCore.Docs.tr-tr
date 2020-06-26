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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: 578263978959100e266626aeccccc0830d9462b7
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399120"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>ASP.NET Core 'de ıhttpclientfactory kullanarak HTTP istekleri yapın

::: moniker range=">= aspnetcore-3.0"

[Glenn CONDRON](https://github.com/glennc), [Ryan şimdi ak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)ve [Kirk larkabağı](https://github.com/serpent5)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> . `IHttpClientFactory`aşağıdaki avantajları sunar:

* , Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` . Örneğin, *GitHub* adlı bir Istemci, [GitHub](https://github.com/)'a erişmek için kaydedilebilir ve yapılandırılabilir. Varsayılan istemci, genel erişim için kaydedilebilir.
* ' De işleyiciler temsilci seçme yoluyla giden ara yazılım kavramını daha da artırır `HttpClient` . ' De işleyiciler temsilci atama avantajlarından faydalanmak için, Polya tabanlı bir ara yazılım için uzantılar sağlar `HttpClient` .
* Temel örneklerin biriktirmesini ve ömrünü yönetir `HttpClientMessageHandler` . Otomatik yönetim, yaşam sürelerini el ile yönetirken oluşan ortak DNS (etki alanı adı sistemi) sorunlarını önler `HttpClient` .
* `ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

Bu konu sürümündeki örnek kod, <xref:System.Text.Json> http yanıtlarında döndürülen JSON içeriğinin serisini kaldırmak için kullanır. Ve kullanan örnekler için `Json.NET` `ReadAsAsync<T>` , bu konunun 2. x sürümünü seçmek üzere sürüm seçiciyi kullanın.

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

En iyi yaklaşım, uygulamanın gereksinimlerine bağlı olarak değişir.

### <a name="basic-usage"></a>Temel kullanım

`IHttpClientFactory`, çağırarak kaydedilebilir `AddHttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

`IHttpClientFactory` [Bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)kullanılarak bir istek yapılabilir. Aşağıdaki kod `IHttpClientFactory` bir örnek oluşturmak için kullanır `HttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory`Yukarıdaki örnekte olduğu gibi kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım hakkında hiçbir etkisi yoktur `HttpClient` . `HttpClient`Var olan bir uygulamada örneklerin oluşturulduğu yerlerde, bu oluşumların ' i çağrılarıyla değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Adlandırılmış istemciler

Adlandırılmış istemciler şu durumlarda iyi bir seçimdir:

* Uygulama birçok farklı kullanımı gerektirir `HttpClient` .
* Birçok `HttpClient` s farklı yapılandırmaya sahiptir.

Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

İstemcinin yapılandırıldığı önceki kodda:

* Temel adres `https://api.github.com/` .
* GitHub API 'SI ile çalışmak için iki üst bilgi gereklidir.

#### <a name="createclient"></a>CreateClient

Her zaman <xref:System.Net.Http.IHttpClientFactory.CreateClient*> çağrılır:

* Yeni bir örneği `HttpClient` oluşturulur.
* Yapılandırma eylemi çağrılır.

Adlandırılmış bir istemci oluşturmak için adını içine geçirin `CreateClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, kod yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` . Örneğin, tek bir türü belirtilmiş istemci kullanılabilir:
  * Tek bir arka uç uç noktası için.
  * Uç nokta ile ilgili tüm mantığı kapsüllemek için.
* DI ile birlikte çalışın ve uygulamada gerektiğinde eklenebilir.

Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Yukarıdaki kodda:

* Yapılandırma, yazılan istemciye taşınır.
* `HttpClient`Nesne bir ortak özellik olarak sunulur.

İşlevselliği kullanıma sunan, API 'ye özgü Yöntemler oluşturulabilir `HttpClient` . Örneğin, `GetAspNetDocsIssues` yöntemi açık sorunları almak için kodu kapsüller.

Aşağıdaki kod, <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> `Startup.ConfigureServices` türü belirtilmiş bir istemci sınıfını kaydetmek için ' de çağırır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yukarıdaki kodda `AddHttpClient` `GitHubService` geçici bir hizmet olarak kaydedilir. Bu kayıt, için bir fabrika yöntemi kullanır:

1. `HttpClient` örneği oluşturun.
1. Örneğini oluşturucusuna geçirerek bir örneğini oluşturun `GitHubService` `HttpClient` .

Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Türü belirtilmiş bir istemcinin yapılandırması `Startup.ConfigureServices` , türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

, `HttpClient` Türü belirlenmiş bir istemci içinde kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran bir yöntem tanımlayın `HttpClient` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Yukarıdaki kodda, `HttpClient` bir özel alanda depolanır. Öğesine erişimi, `HttpClient` genel yöntemi tarafından yapılır `GetRepos` .

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi üçüncü taraf kitaplıklarla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.

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

## <a name="make-post-put-and-delete-requests"></a>GÖNDERI, PUT ve DELETE isteklerini oluşturma

Yukarıdaki örneklerde, tüm HTTP istekleri GET HTTP fiilini kullanır. `HttpClient`, aşağıdakiler de dahil olmak üzere diğer HTTP fiillerini de destekler:

* POST
* PUT
* DELETE
* DÜZELTMESI

Desteklenen HTTP fiillerinin tüm listesi için bkz <xref:System.Net.Http.HttpMethod> ..

Aşağıdaki örnek, bir HTTP POST isteğinin nasıl yapılacağını göstermektedir:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

Yukarıdaki kodda, `CreateItemAsync` yöntemi:

* `TodoItem`Parametresini kullanarak JSON için parametreyi seri hale getirir `System.Text.Json` . Bu <xref:System.Text.Json.JsonSerializerOptions> , serileştirme işlemini yapılandırmak için bir örneğini kullanır.
* <xref:System.Net.Http.StringContent>Http isteğinin gövdesinde göndermek üzere seri hale GETIRILMIŞ JSON paketini paketlemek için bir örneği oluşturur.
* <xref:System.Net.Http.HttpClient.PostAsync%2A>JSON içeriğini BELIRTILEN URL 'ye göndermek için çağrılar. Bu, [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)'e eklenen GÖRELI bir URL 'dir.
* <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>Yanıt durum kodu başarıyı belirtmezse bir özel durum oluşturmak için çağırır.

`HttpClient`, diğer içerik türlerini de destekler. Örneğin, <xref:System.Net.Http.MultipartContent> ve <xref:System.Net.Http.StreamContent> . Desteklenen içeriğin tamamı listesi için bkz <xref:System.Net.Http.HttpContent> ..

Aşağıdaki örnekte bir HTTP PUT isteği gösterilmektedir:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

Yukarıdaki kod, POST örneğine çok benzer. `SaveItemAsync`Yöntemi yerine çağırır <xref:System.Net.Http.HttpClient.PutAsync%2A> `PostAsync` .

Aşağıdaki örnekte bir HTTP SILME isteği gösterilmektedir:

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

Yukarıdaki kodda, `DeleteItemAsync` yöntemi çağırır <xref:System.Net.Http.HttpClient.DeleteAsync%2A> . HTTP DELETE istekleri genellikle gövde içermediğinden, `DeleteAsync` yöntemi bir örneğini kabul eden bir aşırı yükleme sağlamaz `HttpContent` .

İle farklı HTTP fiillerini kullanma hakkında daha fazla bilgi için `HttpClient` bkz <xref:System.Net.Http.HttpClient> ..

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

* Türet <xref:System.Net.Http.DelegatingHandler> .
* Geçersiz kıl <xref:System.Net.Http.DelegatingHandler.SendAsync*> . İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütün:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, üstbilginin istekte olup olmadığını denetler `X-API-KEY` . `X-API-KEY`Eksikse, <xref:System.Net.HttpStatusCode.BadRequest> döndürülür.

İçin yapılandırmasına birden fazla işleyici eklenebilir `HttpClient` <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir. `IHttpClientFactory`Her işleyici için ayrı bır dı kapsamı oluşturur. İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olabilir. İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties)kullanarak işleyicide veri geçirin.
* <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>Geçerli isteğe erişmek için kullanın.
* <xref:System.Threading.AsyncLocal`1>Verileri geçirmek için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`üçüncü taraf kitaplığı [Polly](https://github.com/App-vNext/Polly)ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` . Polly uzantıları, istemcilere Polly tabanlı işleyiciler eklemeyi destekler. Polly, [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini gerektirir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Hatalar genellikle dış HTTP çağrıları geçici olduğunda oluşur. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>geçici hataları işlemek için bir ilkenin tanımlanmasını sağlar. `AddTransientHttpErrorPolicy`Aşağıdaki yanıtları işleyecek şekilde yapılandırılan ilkeler:

* <xref:System.Net.Http.HttpRequestException>
* HTTP 5xx
* HTTP 408

`AddTransientHttpErrorPolicy``PolicyBuilder`olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Uzantı yöntemleri, örneğin, Polly tabanlı işleyiciler eklemek için sağlanır <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*> . Aşağıdaki `AddPolicyHandler` aşırı yükleme, hangi ilkenin uygulanacağını belirlemek için isteği inceler:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Polly ilkeleri iç içe almak yaygın bir şekilde yapılır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte:

* İki işleyici eklenir.
* İlk işleyici, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> yeniden deneme ilkesi eklemek için kullanır. Başarısız istekler en fazla üç kez yeniden denenir.
* İkinci `AddTransientHttpErrorPolicy` çağrı bir devre kesici ilkesi ekler. 5 başarısız girişim sıralı olarak gerçekleşirse, daha fazla dış istek 30 saniye boyunca engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` .

Aşağıdaki kodda:

* "Normal" ve "uzun" ilkeler eklenmiştir.
* <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>kayıt defterinden "normal" ve "uzun" ilkeleri ekler.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

Ve daha fazla tümleştirme hakkında daha fazla bilgi için `IHttpClientFactory` bkz. [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` . <xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına oluşturulur. Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .

`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS (etki alanı adı sistemi) değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

`HttpClient`örnekler genellikle aktiften **çıkarma gerektirmeyen .NET nesneleri olarak** kabul edilebilir. Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> . `IHttpClientFactory`örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` .

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` . Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:

* Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .
* Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .

Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .

- `SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.
- `HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .

Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` . Bu paylaşım, yuva azalmasına engel olur.
- `SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmasında uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, "System .net. http. HttpClient" kategorisine sahip iletileri günlüğe kaydeder. **Mynamedclient**. LogicalHandler ". *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu Iletiler "System .net. http. HttpClient" günlük kategorisiyle günlüğe kaydedilir. **Mynamedclient**. ClientHandler ". İstek için bu, tüm diğer işleyiciler çalıştırıldıktan sonra ve istek gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, istek üst bilgilerinde veya yanıt durum kodunda yapılan değişiklikleri içerebilir.

İstemcinin adını log kategorisinde da içermek, belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .

`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` . `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üst bilgi yayma ara yazılımı

Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymaya yönelik bir ASP.NET Core ara istemcindedir. Üst bilgi yaymayı kullanmak için:

* [Microsoft. AspNetCore. Headeryayma](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) paketine başvurun.
* Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :

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

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> . Aşağıdaki avantajları sunar:

* , Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` . Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir. Varsayılan istemci, diğer amaçlar için kaydedilebilir.
* ' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.
* `HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .
* `ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden tamamen üst değildir. En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir. `IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` . `HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır. Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir. Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.

Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.

Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` . Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` . Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.
* DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.

Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. `HttpClient`Nesne bir ortak özellik olarak sunulur. İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` . `GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.

Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda, `HttpClient` bir özel alan olarak depolanır. Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.

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

Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> . `SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, temel bir işleyiciyi tanımlar. İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` . Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` . Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir. `IHttpClientFactory`Her işleyici için ayrı bır dı kapsamı oluşturur. İşleyiciler herhangi bir kapsamın hizmetlerine bağlı olarak ücretsizdir. İşleyicilerin bağımlı olduğu hizmetler, işleyicinin elden çıkarılmasıyla kaldırılır.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyicinin türü geçirerek, çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .
* `IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.
* `AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` . Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.
* , [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur. `AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir. Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.

`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` . Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır. Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır. Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte, iki işleyici eklenmiştir. İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır. Başarısız istekler en fazla üç kez yeniden denenir. İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler. Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` . Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` . Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.

Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` . <xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına. Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .

`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir. Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin çıkarılması gerekli değildir. Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> . `IHttpClientFactory`örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` . `HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` . Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:

* Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .
* Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .

Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .

- `SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.
- `HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .

Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` . Bu paylaşım, yuva azalmasına engel olur.
- `SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.

İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .

`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` . `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Dayanıklı HTTP isteklerini uygulamak için HttpClientFactory kullanma](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [HttpClientFactory ve Polly ilkeleriyle üstel geri alma ile HTTP çağrı yeniden denemeleri uygulayın](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Devre Kesici desenini uygulama](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

, [Glenn CONDRON](https://github.com/glennc), [Ryan şimdi e](https://github.com/rynowak)ve [Steve Gordon](https://github.com/stevejgordon)

Bir <xref:System.Net.Http.IHttpClientFactory> uygulamadaki örnekleri yapılandırmak ve oluşturmak için kayıt yapılabilir ve kullanılabilir <xref:System.Net.Http.HttpClient> . Aşağıdaki avantajları sunar:

* , Mantıksal örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar `HttpClient` . Örneğin, *GitHub istemcisi kayıtlı* ve [GitHub](https://github.com/)'a erişebilecek şekilde yapılandırılabilir. Varsayılan istemci, diğer amaçlar için kaydedilebilir.
* ' De işleyiciler için temsilci atama ile giden ara yazılım kavramı `HttpClient` ve bundan faydalanmak için, Polly tabanlı ara yazılım için uzantılar sağlar.
* `HttpClientMessageHandler`Yaşam sürelerini el ile yönetirken gerçekleşen YAYGıN DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir `HttpClient` .
* `ILogger`Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

.NET Framework hedefleyen projeler [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet paketinin yüklenmesini gerektirir. .NET Core ile hedeflenen ve [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) 'e başvuran projeler zaten paketi içeriyor `Microsoft.Extensions.Http` .

## <a name="consumption-patterns"></a>Tüketim desenleri

Bir uygulamada çeşitli yollar `IHttpClientFactory` kullanılabilir:

* [Temel kullanım](#basic-usage)
* [Adlandırılmış istemciler](#named-clients)
* [Yazılan istemciler](#typed-clients)
* [Oluşturulan istemciler](#generated-clients)

Hiçbiri diğerinden tamamen üst değildir. En iyi yaklaşım, uygulamanın kısıtlamalarına bağlıdır.

### <a name="basic-usage"></a>Temel kullanım

, `IHttpClientFactory` `AddHttpClient` Yöntemi içindeki içindeki genişletme yöntemi çağırarak kaydedilebilir `IServiceCollection` `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Kaydedildikten sonra kod, `IHttpClientFactory` [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile her yerden bir hizmeti kabul edebilir. `IHttpClientFactory`Bir örnek oluşturmak için kullanılabilir `HttpClient` :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory`Bu biçimde kullanmak, mevcut bir uygulamayı yeniden düzenleme için iyi bir yoldur. Kullanım şekli üzerinde hiçbir etkisi yoktur `HttpClient` . `HttpClient`Örneklerin Şu anda oluşturulduğu yerlerde, bu tekrarlamaları ' a çağrı ile değiştirin <xref:System.Net.Http.IHttpClientFactory.CreateClient*> .

### <a name="named-clients"></a>Adlandırılmış istemciler

Bir uygulama `HttpClient` , her biri farklı bir yapılandırmaya sahip birçok farklı kullanım gerektiriyorsa, **adlandırılmış istemciler**kullanılır. Adlandırılmış için yapılandırma `HttpClient` , içinde kayıt sırasında belirtilebilir `Startup.ConfigureServices` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Yukarıdaki kodda, `AddHttpClient` *GitHub*adının sağlanması denir. Bu istemci, &mdash; GITHUB API 'siyle birlikte çalışmak için gerekli olan temel adres ve iki üst bilgi olan bazı varsayılan yapılandırma uygulanmış.

Her seferinde her `CreateClient` çağrıldığında yeni bir örneği `HttpClient` oluşturulur ve yapılandırma eylemi çağrılır.

Adlandırılmış bir istemciyi kullanmak için, bir dize parametresi iletilebilir `CreateClient` . Oluşturulacak istemcinin adını belirtin:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Yukarıdaki kodda, isteğin bir ana bilgisayar adı belirtmesi gerekmez. İstemci için yapılandırılan taban adresi kullanıldığından, bu yalnızca yolu geçirebilir.

### <a name="typed-clients"></a>Yazılan istemciler

Yazılan istemciler:

* Dizeleri anahtar olarak kullanma gereksinimi olmadan, adlandırılmış istemcilerle aynı özellikleri sağlayın.
* İstemcileri tükettiren IntelliSense ve derleyici yardımı sağlar.
* Yapılandırmak ve belirli bir ile etkileşimde bulunmak için tek bir konum belirtin `HttpClient` . Örneğin, tek bir arka uç uç noktası için tek bir adet yazılmış istemci kullanılabilir ve bu uç nokta ile ilgili tüm mantığı kapsüllenebilir.
* DI ile birlikte çalışın ve uygulamanızda gerektiğinde eklenebilir.

Türü belirtilmiş istemci `HttpClient` , oluşturucusunda bir parametreyi kabul eder:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Önceki kodda, yapılandırma yazılan istemciye taşınır. `HttpClient`Nesne bir ortak özellik olarak sunulur. İşlevselliği kullanıma sunan API 'ye özel yöntemler tanımlamak mümkündür `HttpClient` . `GetAspNetDocsIssues`Yöntemi, GitHub deposundan en son açık sorunları sorgulamak ve ayrıştırmak için gereken kodu saklar.

Türü belirtilmiş bir istemciyi kaydettirmek için, genel <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genişletme yöntemi içinde kullanılabilir `Startup.ConfigureServices` istemci sınıfını belirterek kullanılabilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Yazılan istemci, DI ile geçici olarak kaydedilir. Yazılan istemci doğrudan eklenebilir ve tüketilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Tercih edilirse, yazılan istemcinin yapılandırması, `Startup.ConfigureServices` türü belirlenmiş istemcinin Oluşturucusu yerine kayıt sırasında belirtilebilir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

`HttpClient`Türü belirtilmiş bir istemci içinde tamamen kapsüllenebilir. Bunu bir özellik olarak göstermek yerine, örneği dahili olarak çağıran ortak Yöntemler sunulabilir `HttpClient` .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Önceki kodda, `HttpClient` bir özel alan olarak depolanır. Dış çağrıları yapmak için tüm erişim `GetRepos` yönteminden geçer.

### <a name="generated-clients"></a>Oluşturulan istemciler

`IHttpClientFactory`, [yeniden sığdırma](https://github.com/paulcbetts/refit)gibi diğer üçüncü taraf kitaplıklarıyla birlikte kullanılabilir. Yeniden sığdırma, .NET için bir REST kitaplığıdır. REST API 'Leri canlı arabirimlere dönüştürür. Arabirim bir uygulama, `RestService` `HttpClient` dış http çağrıları yapmak için kullanılarak tarafından dinamik olarak oluşturulur.

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

Bir işleyici oluşturmak için, öğesinden türeten bir sınıf tanımlayın <xref:System.Net.Http.DelegatingHandler> . `SendAsync`İsteği ardışık düzen içindeki bir sonraki işleyiciye geçirmeden önce kodu yürütmek için yöntemi geçersiz kılın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Yukarıdaki kod, temel bir işleyiciyi tanımlar. İsteğe bağlı bir başlık olup olmadığını denetler `X-API-KEY` . Üst bilgi eksikse, HTTP çağrısından kaçınabilir ve uygun bir yanıt döndürebilir.

Kayıt sırasında, bir veya daha fazla işleyici bir için yapılandırmasına eklenebilir `HttpClient` . Bu görev, üzerindeki genişletme yöntemleri aracılığıyla gerçekleştirilir <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> .

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

Yukarıdaki kodda,, `ValidateHeaderHandler` dı ile kaydedilir. İşleyicinin, bir geçici hizmet olarak dı 'ye kayıtlı olması **gerekir** , hiçbir koşulda kapsamı yoktur. İşleyici kapsamlı bir hizmet olarak kayıtlıysa ve işleyicinin bağımlı olduğu tüm hizmetler atılabilir olur:

* İşleyici kapsam dışına geçmeden önce işleyicinin Hizmetleri atılamaz.
* Atılmış işleyici Hizmetleri işleyicinin başarısız olmasına neden olur.

Kaydedildikten sonra, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> işleyici türünü geçirerek çağrılabilir.

Birden çok işleyici, yürütülmesi gereken sırayla kaydedilebilir. Her işleyici, son isteği çalıştırana kadar sonraki işleyiciyi sarmalar `HttpClientHandler` :

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

İleti işleyicileriyle istek başına durumu paylaşmak için aşağıdaki yaklaşımlardan birini kullanın:

* Kullanarak işleyicide veri geçirin `HttpRequestMessage.Properties` .
* `IHttpContextAccessor`Geçerli isteğe erişmek için kullanın.
* `AsyncLocal`Verileri geçirmek için özel bir depolama nesnesi oluşturun.

## <a name="use-polly-based-handlers"></a>Polly tabanlı işleyiciler kullanın

`IHttpClientFactory`, [Polly](https://github.com/App-vNext/Polly)adlı popüler bir üçüncü taraf kitaplığı ile tümleşir. Polly, .NET için kapsamlı bir esnekliği ve geçici hata işleme kitaplığıdır. Geliştiricilerin yeniden deneme, devre kesici, zaman aşımı, Bulkbaş yalıtımı, akıcı ve iş parçacığı açısından güvenli bir şekilde geri dönüş gibi ilkeler almasına olanak tanır.

Uzantı yöntemleri, yapılandırılmış örneklerle Polly ilkelerin kullanımını etkinleştirmek için sağlanır `HttpClient` . Polly uzantıları:

* İstemcilere Polly tabanlı işleyiciler eklemeyi destekler.
* , [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet paketini yükledikten sonra kullanılabilir. Paket, ASP.NET Core paylaşılan çerçevesine dahil değildir.

### <a name="handle-transient-faults"></a>Geçici hataları işle

Yaygın hatalar, dış HTTP çağrıları geçici olduğunda oluşur. `AddTransientHttpErrorPolicy`Geçici hataları işlemek üzere bir ilkenin tanımlanmasını sağlayan, çağrılan bir uygun genişletme yöntemi eklenmiştir. Bu uzantı yöntemi tanıtıcısıyla yapılandırılmış ilkeler `HttpRequestException` , http 5xx yanıtları ve http 408 yanıtları.

`AddTransientHttpErrorPolicy`Uzantı içinde kullanılabilir `Startup.ConfigureServices` . Uzantı, `PolicyBuilder` olası bir geçici hatayı temsil eden hataları işlemek için yapılandırılmış bir nesneye erişim sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Yukarıdaki kodda bir `WaitAndRetryAsync` ilke tanımlanmıştır. Başarısız istekler, denemeler arasındaki 600 MS gecikmeyle en fazla üç kez yeniden denenir.

### <a name="dynamically-select-policies"></a>Dinamik olarak ilke seçme

Polly tabanlı işleyiciler eklemek için kullanılabilecek ek uzantı yöntemleri vardır. Bu tür bir uzantının `AddPolicyHandler` birden çok aşırı yüklemesi vardır. Bir aşırı yükleme, hangi ilkenin uygulanacağını tanımlarken isteğin incelenebilirliğini sağlar:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Yukarıdaki kodda, giden istek bir HTTP GET ise, 10 saniyelik bir zaman aşımı uygulanır. Diğer HTTP yöntemleri için, 30 saniyelik bir zaman aşımı kullanılır.

### <a name="add-multiple-polly-handlers"></a>Birden çok Polly işleyici ekleme

Gelişmiş işlevsellik sağlamak için çok fazla ilke iç içe geçmiş bir yaygın hale gelir:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Yukarıdaki örnekte, iki işleyici eklenmiştir. İlki, `AddTransientHttpErrorPolicy` yeniden deneme ilkesi eklemek için uzantıyı kullanır. Başarısız istekler en fazla üç kez yeniden denenir. İçin ikinci çağrı, `AddTransientHttpErrorPolicy` bir devre kesici ilkesi ekler. Beş başarısız girişim sırayla gerçekleşiyorsa, daha fazla dış istek 30 saniye için engellenir. Devre kesici ilkeleri durum bilgisi vardır. Bu istemci aracılığıyla yapılan tüm çağrılar aynı devre durumunu paylaşır.

### <a name="add-policies-from-the-polly-registry"></a>Polly kayıt defterinden ilke ekleme

Düzenli olarak kullanılan ilkeleri yönetmeye yönelik bir yaklaşım, bunları bir kez tanımlayıp bir ile kaydetmektir `PolicyRegistry` . Kayıt defterinden bir ilke kullanılarak bir işleyicinin eklenmesine izin veren bir genişletme yöntemi sağlanır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Yukarıdaki kodda, öğesine eklendiğinde iki ilke kaydedilir `PolicyRegistry` `ServiceCollection` . Kayıt defterinden bir ilke kullanmak için `AddPolicyHandlerFromRegistry` yöntemi kullanılır ve uygulanacak ilke adı geçer.

Ve daha fazla tümleştirme hakkında daha fazla bilgi `IHttpClientFactory` , [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)' de bulunabilir.

## <a name="httpclient-and-lifetime-management"></a>HttpClient ve ömür yönetimi

`HttpClient`Her çağrıldığında yeni bir örnek döndürülür `CreateClient` `IHttpClientFactory` . <xref:System.Net.Http.HttpMessageHandler>Adlandırılmış istemci başına. Fabrika, örneklerin yaşam sürelerini yönetir `HttpMessageHandler` .

`IHttpClientFactory``HttpMessageHandler`kaynak tüketimini azaltmak için fabrika tarafından oluşturulan örnekleri havuzlar. Bir `HttpMessageHandler` örnek, süresi dolmamışsa yeni bir örnek oluştururken havuzdan yeniden kullanılabilir `HttpClient` .

Her işleyici genellikle kendi temel HTTP bağlantılarını yönettiğinden, işleyicilerin havuzlaması tercih edilir. Gerekenden daha fazla işleyici oluşturulması bağlantı gecikmeleri oluşmasına neden olabilir. Ayrıca, bazı işleyiciler bağlantıları süresiz olarak açık tutar, bu da işleyicinin DNS değişikliklerine yeniden davranmasını engelleyebilir.

Varsayılan işleyici ömrü iki dakikadır. Varsayılan değer, adlandırılmış istemci temelinde geçersiz kılınabilir. Bunu geçersiz kılmak için, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> `IHttpClientBuilder` istemcisini oluştururken döndürülen öğesini çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

İstemcinin çıkarılması gerekli değildir. Çıkarma giden istekleri iptal eder ve `HttpClient` çağırma sonrasında verilen örneğin kullanılamaz olmasını sağlar <xref:System.IDisposable.Dispose*> . `IHttpClientFactory`örnekler tarafından kullanılan kaynakları izler ve ortadan kaldırdık `HttpClient` . `HttpClient`Örnekler genellikle aktiften çıkarma gerektirmeyen .NET nesneleri olarak kabul edilebilir.

Tek `HttpClient` bir örneğinin uzun süre canlı tutulması, önünde kullanılmadan önce kullanılan ortak bir modeldir `IHttpClientFactory` . Bu model, ' a geçtikten sonra gereksiz hale gelir `IHttpClientFactory` .

### <a name="alternatives-to-ihttpclientfactory"></a>Ihttpclientfactory alternatifleri

`IHttpClientFactory`Dı etkin bir uygulamada kullanmak şunları önler:

* Havuz örneklerine göre kaynak tükenmesi sorunları `HttpMessageHandler` .
* Düzenli aralıklarla örnekleri geçirerek eski DNS sorunları `HttpMessageHandler` .

Uzun süreli bir örnek kullanarak önceki sorunları çözmenin alternatif yolları vardır <xref:System.Net.Http.SocketsHttpHandler> .

- `SocketsHttpHandler`Uygulamanın başladığı zaman bir örneği oluşturun ve uygulamanın ömrü boyunca kullanın.
- <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>DNS yenileme süreleri temelinde uygun bir değere yapılandırın.
- `HttpClient`Gerektiğinde örnek oluşturun `new HttpClient(handler, disposeHandler: false)` .

Yukarıdaki yaklaşımlar, `IHttpClientFactory` benzer bir şekilde çözen kaynak yönetimi sorunlarını çözer.

- , `SocketsHttpHandler` Örnekleri arasında bağlantıları paylaşır `HttpClient` . Bu paylaşım, yuva azalmasına engel olur.
- `SocketsHttpHandler`Bağlantıları, `PooledConnectionLifetime` eski DNS sorunlarından kaçınmak için öğesine göre döngüler.

### <a name="cookies"></a>Tanımlama bilgileri

Havuza alınmış `HttpMessageHandler` örnekler, `CookieContainer` paylaşılan nesneler ile sonuçlanır. Beklenmeyen `CookieContainer` nesne paylaşımı genellikle hatalı kodla sonuçlanır. Tanımlama bilgileri gerektiren uygulamalar için şunlardan birini göz önünde bulundurun:

 - Otomatik tanımlama bilgisi işlemeyi devre dışı bırakma
 - Önlemenin`IHttpClientFactory`

<xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Otomatik tanımlama bilgisi işlemesini devre dışı bırakmak için çağırın:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a>Günlüğe Kaydetme

`IHttpClientFactory`Tüm istekler için kayıt günlüğü iletileri aracılığıyla oluşturulan istemciler. Varsayılan günlük iletilerini görmek için günlük yapılandırmanızda uygun bilgi düzeyini etkinleştirin. İstek üst bilgilerinin günlüğe kaydedilmesi gibi ek Günlükler yalnızca izleme düzeyinde yer alır.

Her istemci için kullanılan günlük kategorisi, istemcinin adını içerir. Örneğin, *Mynamedclient*adlı bir istemci, bir kategorisine sahip iletileri günlüğe kaydeder `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler` . *Logicalhandler* ile düzeltilen iletiler istek işleyicisi ardışık düzeni dışında oluşur. İstekte, işlem hattındaki diğer işleyiciler işlenmeden önce iletiler günlüğe kaydedilir. Yanıtta, tüm diğer işlem hattı işleyicileri yanıtı aldıktan sonra iletiler günlüğe kaydedilir.

Günlüğe kaydetme, istek işleyicisi ardışık düzeni içinde de gerçekleşir. *Mynamedclient* örneğinde, bu iletiler günlük kategorisine göre günlüğe kaydedilir `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` . İstek için bu, tüm diğer işleyiciler çalıştıktan sonra ve istek ağda gönderilmeden hemen önce gerçekleşir. Yanıtta, bu günlüğe kaydetme, işleyicinin işleyici işlem hattı üzerinden geri geçirmeden önce yanıtın durumunu içerir.

İşlem hattının dışında ve içinde günlüğe kaydetmenin etkinleştirilmesi, diğer işlem hattı işleyicileri tarafından yapılan değişikliklerin incelemesini etkinleştirir. Bu, örneğin veya yanıt durum kodunda istek başlıklarındaki değişiklikleri içerebilir.

İstemci adı ' nı log kategorisinde da içermek, gerektiğinde belirli adlandırılmış istemciler için günlük filtrelemeyi sunar.

## <a name="configure-the-httpmessagehandler"></a>HttpMessageHandler 'ı yapılandırma

İstemci tarafından kullanılan iç yapılandırmayı denetlemek gerekli olabilir `HttpMessageHandler` .

`IHttpClientBuilder`Adlandırılmış veya yazılan istemciler eklenirken bir döndürülür. <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>Genişletme yöntemi bir temsilciyi tanımlamak için kullanılabilir. Temsilci, `HttpMessageHandler` Bu istemci tarafından kullanılan birincili oluşturmak ve yapılandırmak için kullanılır:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Konsol uygulamasında ıhttpclientfactory kullanma

Konsol uygulamasında, aşağıdaki paket başvurularını projeye ekleyin:

* [Microsoft. Extensions. Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Aşağıdaki örnekte:

* <xref:System.Net.Http.IHttpClientFactory>, [genel konağın](xref:fundamentals/host/generic-host) hizmet kapsayıcısına kaydedilir.
* `MyService`hizmetinden bir istemci fabrikası örneği oluşturur `HttpClient` . `HttpClient`, bir Web sayfasını almak için kullanılır.
* `Main`hizmetin yöntemini yürütmek için bir kapsam oluşturur `GetPage` ve Web sayfası içeriğinin ilk 500 karakterini konsola yazar.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a>Üst bilgi yayma ara yazılımı

Üst bilgi yayma, gelen istekten giden HTTP Istemci isteklerine HTTP üstbilgilerini yaymak için bir topluluk tarafından desteklenen bir ara yazılımlar. Üst bilgi yaymayı kullanmak için:

* Topluluğun, paket [Headeryayılmasının](https://www.nuget.org/packages/HeaderPropagation)desteklediği bağlantı noktasına başvurun. ASP.NET Core 3,1 ve üzeri, [Microsoft. AspNetCore. Headeryayılmayı](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)destekler.

* Ara yazılımı ve `HttpClient` içinde yapılandırın `Startup` :

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
