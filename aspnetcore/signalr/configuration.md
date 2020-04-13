---
title: ASP.NET SignalR Core yapılandırması
author: bradygaster
description: ASP.NET Core SignalR uygulamalarını nasıl yapılandırıştırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223991"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR yapılandırması

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html) Her protokolün serileştirme yapılandırma seçenekleri vardır.

JSON [serileştirme, AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir. `AddJsonProtocol`[addSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) sonra `Startup.ConfigureServices`eklenebilir. Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır. Bu nesnedeki [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir. Daha fazla bilgi için [System.Text.Json belgelerine](/dotnet/api/system.text.json)bakın.

Örnek olarak, varsayılan "camelCase" adları yerine özellik adlarının kasasını değiştirmemek için serileştiriciyi yapılandırmak `Startup.ConfigureServices`için aşağıdaki kodu kullanın:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır. Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Json'a geç

Eğer bu özellikleri `Newtonsoft.Json` desteklenmez `System.Text.Json`gerekiyorsa, [Newtonsoft.Json geçiş](xref:migration/22-to-30#switch-to-newtonsoftjson)bakın.

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir. Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder. Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer, değerin `KeepAliveInterval` iki katıdır.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `KeepAliveInterval` | 15 saniye | Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir. Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir. |
| `EnableDetailedErrors` | `false` | Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse. Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi. |
| `StreamBufferCapacity` | `10` | İstemci yükleme akışları için arabelleğe alınabilecek maksimum öğe sayısı. Bu sınıra ulaşılırsa, sunucu öğeleri akış işlemlerine kadar çağrıların işlenmesi engellenir.|
| `MaximumReceiveMessageSize` | 32 KB | Tek bir gelen hub iletisinin maksimum boyutu. |

Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions` Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Arka basınç uygulamadan önce sunucunun arabellek yaptığı istemciden alınan maksimum bayt sayısı. Bu değeri artırmak, sunucunun geri baskı uygulamadan daha büyük iletileri daha hızlı almasını sağlar, ancak bellek tüketimini artırabilir. |
| `AuthorizationData` | Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler. | İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Arka basıncı gözlemlemeden önce sunucunun arabellek yaptığı uygulama tarafından gönderilen maksimum bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletileri geri baskı beklemeden daha hızlı arabelleğe almamasına olanak tanır, ancak bellek tüketimini artırabilir. |
| `Transports` | Tüm Taşımalar etkinleştirildi. | Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder. |
| `LongPolling` | Aşağıya bakın. | Uzun Yoklama taşımasına özel ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets aktarımına özel ek seçenekler. |

Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre. Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur. |

WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.

### <a name="configure-logging"></a>Günlük işlemlerini yapılandırma

Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir. Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.

> [!NOTE]
> Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir. Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin. Uzantı `AddConsole` yöntemini arayın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır. Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın. Günlükler tarayıcı konsolpenceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Bir `LogLevel` değer yerine, günlük düzeyi `string` adını temsil eden bir değer de sağlayabilirsiniz. Bu, `LogLevel` sabitlere erişiminiz olmayan ortamlarda SignalR günlüğe kaydetmeyi yapılandırırken kullanışlıdır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir. Günlüğe kaydedilecek `configureLogging` **minimum** günlük düzeyini ayarlar için sağladığınız değer. Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.

| Dize                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**veya**`information` | `LogLevel.Information` |
| `warn`**veya**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.

SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır. Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir. Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir. İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir. Tüm aktarımlar varsayılan olarak etkinleştirilir.

Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.

Java istemcisinde, aktarım `withTransport` `HttpHubConnectionBuilder`. Java istemcisi varsayılan olarak WebSockets aktarımını kullanır.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java istemcisi henüz geri dönüş taşıma yı desteklemez.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın. .NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması). JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır. Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.

.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz. [Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma

Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose` Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET Seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `ClientCertificates` | Boş | İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu. |
| `Cookies` | Boş | Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu. |
| `Credentials` | Boş | Her HTTP isteğiyle gönderilecek kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |
| `HttpMessageHandlerFactory` | `null` | HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci. WebSocket bağlantıları için kullanılmaz. Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır. Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün. **İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.** |
| `Proxy` | `null` | HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si. |
| `UseDefaultCredentials` | `false` | BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını sağlar. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java Seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `withHeader` `withHeaders` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |

---

.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html) Her protokolün serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir. Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır. Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir. Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.
 
Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır. Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir. Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta bir ileti (canlı tutma dahil) almamışsa istemcinin bağlantısını kesmiş olarak kabul eder. Bu uygulama nın nasıl uygulandığı nedeniyle istemcinin gerçekten bağlantısız olarak işaretlenolması bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer, değerin `KeepAliveInterval` iki katıdır.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `KeepAliveInterval` | 15 saniye | Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir. Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir. |
| `EnableDetailedErrors` | `false` | Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse. Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi. |

Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions` Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Sunucu arabellekten alınan en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. |
| `AuthorizationData` | Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler. | İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir. |
| `Transports` | Tüm Taşımalar etkinleştirildi. | Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder. |
| `LongPolling` | Aşağıya bakın. | Uzun Yoklama taşımasına özel ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets aktarımına özel ek seçenekler. |

Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre. Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur. |

WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.

### <a name="configure-logging"></a>Günlük işlemlerini yapılandırma

Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir. Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.

> [!NOTE]
> Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir. Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin. Uzantı `AddConsole` yöntemini arayın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır. Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın. Günlükler tarayıcı konsolpenceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.

SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır. Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir. Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir. İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir. Tüm aktarımlar varsayılan olarak etkinleştirilir.

Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java istemci websockets bu sürümünde sadece kullanılabilir aktarım.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın. .NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması). JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır. Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.

.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz. [Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma

Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose` Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti göndermek zamanlayıcıyı aralığın başlangıcına sıfırlar. İstemci sunucuda `ClientTimeoutInterval` kümede bir ileti göndermediyse, sunucu istemcinin bağlantısını kestirmiş olarak kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET Seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `ClientCertificates` | Boş | İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu. |
| `Cookies` | Boş | Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu. |
| `Credentials` | Boş | Her HTTP isteğiyle gönderilecek kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |
| `HttpMessageHandlerFactory` | `null` | HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci. WebSocket bağlantıları için kullanılmaz. Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır. Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün. **İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.** |
| `Proxy` | `null` | HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si. |
| `UseDefaultCredentials` | `false` | BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını sağlar. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java Seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `withHeader` `withHeaders` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |

---

.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR mesajları kodlamak için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack.](https://msgpack.org/index.html) Her protokolün serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, addsignalr yönteminizden `Startup.ConfigureServices` sonra eklenebilecek [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) uzantı yöntemi kullanılarak sunucuda yapılandırılabilir. Yöntem, `AddJsonProtocol` bir `options` nesne alan bir temsilci alır. Bu nesnedeki [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, `JsonSerializerSettings` bağımsız değişkenlerin ve döndürülme değerlerinin serileştirilmesini yapılandırmak için kullanılabilecek JSON.NET bir nesnedir. Daha fazla bilgi için [JSON.NET belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.
 
Örnek olarak, serializer'ı varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde `Startup.ConfigureServices`yapılandırmak için aşağıdaki kodu kullanın:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET istemcisinde, `AddJsonProtocol` [hubconnectionbuilder'da](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)aynı uzantı yöntemi vardır. Uzantı `Microsoft.Extensions.DependencyInjection` yöntemini çözmek için ad alanı nın içe aktarılması gerekir:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Şu anda JavaScript istemcisinde JSON serileştirmeyapılandırmak mümkün değildir.

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack [serileştirme, AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlayarak yapılandırılabilir. Daha fazla bilgi için [SignalR'da MessagePack'e](xref:signalr/messagepackhubprotocol) bakın.

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirmesini yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda SignalR hub'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığında ilk el sıkışma iletisi göndermezse, bağlantı kapatılır. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |
| `KeepAliveInterval` | 15 saniye | Sunucu bu aralıkta bir ileti göndermediyse, bağlantıyı açık tutmak için otomatik olarak bir ping iletisi gönderilir. Değiştirirken, `KeepAliveInterval`istemcinin ayarını `ServerTimeout` / `serverTimeoutInMilliseconds` değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer, değerin `KeepAliveInterval` iki katıdır.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak protokoller tek tek hub'lar için belirli protokolleri devre dışı kaldırmak için bu listeden kaldırılabilir. |
| `EnableDetailedErrors` | `false` | Hub `true`yönteminde özel bir özel durum atıldığında, ayrıntılı özel durum iletileri istemcilere döndürülürse. Varsayılan, `false`bu özel durum iletileri hassas bilgiler içerebilir gibi. |

Seçenekler, çağrıya bir seçenek temsilcisi sağlayarak tüm hub'lar için `AddSignalR` `Startup.ConfigureServices`yapılandırılabilir.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Tek bir hub için seçenekler, sağlanan `AddSignalR` genel seçenekleri geçersiz <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>kılar ve aşağıdakiler kullanılarak yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. `HttpConnectionDispatcherOptions` Bu [seçenekler, maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek `Startup.Configure`yapılandırılır.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Aşağıdaki tabloda, Core SignalR'ın gelişmiş HTTP seçeneklerini yapılandırma ASP.NET seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Sunucu arabellekten alınan en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. |
| `AuthorizationData` | Hub sınıfına `Authorize` uygulanan özniteliklerden otomatik olarak toplanan veriler. | İstemcinin hub'a bağlanma yetkisi olup olmadığını belirlemek için kullanılan [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Sunucu arabellekyaptığı uygulama tarafından gönderilen maksimum bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler göndermesine olanak tanır, ancak bellek tüketimini olumsuz etkileyebilir. |
| `Transports` | Tüm Taşımalar etkinleştirildi. | Bir bit, istemcinin `HttpTransportType` bağlanmak için kullanabileceği aktarımları kısıtlayabilen değerlerin eum'una işaret eder. |
| `LongPolling` | Aşağıya bakın. | Uzun Yoklama taşımasına özel ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets aktarımına özel ek seçenekler. |

Uzun Yoklama aktarım özelliği kullanılarak `LongPolling` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Sunucu, tek bir anket isteğini sonlandırmadan önce istemciye ileti gönderilmesini beklediği maksimum süre. Bu değerin azaltılması istemcinin yeni anket isteklerini daha sık yayınlamasına neden olur. |

WebSocket aktarım özelliği kullanılarak `WebSockets` yapılandırılabilir ek seçenekler vardır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapatamazsa, bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | Üstbilgiyi `Sec-WebSocket-Protocol` özel bir değere ayarlamak için kullanılabilecek bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri türüne `HubConnectionBuilder` göre yapılandırılabilir (.NET ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de kullanılabilir, ancak `HttpHubConnectionBuilder` alt sınıf oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisinin de üzerinde dir.

### <a name="configure-logging"></a>Günlük işlemlerini yapılandırma

Günlük, `ConfigureLogging` .NET İstemci yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreleri sunucuda olduğu gibi kaydedilebilir. Daha fazla bilgi için [ASP.NET Çekirdek belgelerinde Günlüğe Kaydetme'ye](xref:fundamentals/logging/index) bakın.

> [!NOTE]
> Günlük sağlayıcılarını kaydetmek için gerekli paketleri yüklemeniz gerekir. Tam liste için dokümanların [Yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, Konsol günlüğe kaydetmeyi `Microsoft.Extensions.Logging.Console` etkinleştirmek için NuGet paketini yükleyin. Uzantı `AddConsole` yöntemini arayın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde de `configureLogging` benzer bir yöntem vardır. Üretecek `LogLevel` günlük iletilerinin minimum düzeyini gösteren bir değer sağlayın. Günlükler tarayıcı konsolpenceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Günlüğe kaydetmeyi tamamen `signalR.LogLevel.None` devre `configureLogging` dışı kılmış için yöntemde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için [SignalR Diagnostics belgelerine](xref:signalr/diagnostics)bakın.

SignalR Java istemcisi günlüğe kaydetme için [SLF4J](https://www.slf4j.org/) kitaplığını kullanır. Kitaplığın kullanıcılarının belirli bir günlük bağımlılığı getirerek kendi özel günlük uygulamalarını seçmelerine olanak tanıyan üst düzey bir günlük API'sidir. Aşağıdaki kod snippet SignalR `java.util.logging` Java istemcisi ile nasıl kullanılacağını gösterir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınızda günlüğe kaydetmeyi yapılandırmazsanız, SLF4J varsayılan bir işlem yazmaz kaydedicisini aşağıdaki uyarı iletisiyle yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan taşımalar `WithUrl` çağrıda (JavaScript'te)`withUrl` yapılandırılabilir. İstemcinin yalnızca belirtilen `HttpTransportType` aktarımları kullanmasını kısıtlamak için biraz akıllıca-OR değerleri kullanılabilir. Tüm aktarımlar varsayılan olarak etkinleştirilir.

Örneğin, Sunucu tarafından Gönderilen Etkinlikler aktarımını devre dışı bırakıp, WebSockets ve Uzun Yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde, aktarımlar alanı sağlanan `transport` seçenekler nesnesi `withUrl`üzerinde ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

SignalR istekleriyle birlikte kimlik doğrulama verileri `AccessTokenProvider` sağlamak`accessTokenFactory` için, istenen erişim belirteci döndüren bir işlev belirtmek için (JavaScript) seçeneğini kullanın. .NET İstemci'sinde, bu erişim belirteci http "Taşıyıcı Kimlik Doğrulaması" belirteci olarak geçirilir `Authorization` (üstbilginin `Bearer`bir türü yle kullanılması). JavaScript istemcisinde, erişim belirteci, tarayıcı API'lerinin üstbilgi uygulama özelliğini kısıtladığı birkaç durum **dışında** (özellikle Sunucu Tarafından Gönderilen Etkinlikler ve WebSockets isteklerinde) Taşıyıcı belirteci olarak kullanılır. Bu gibi durumlarda, erişim belirteci bir `access_token`sorgu dize değeri olarak sağlanır.

.NET istemcisinde, `AccessTokenProvider` seçenek aşağıdaki seçenekler temsilcisi kullanılarak `WithUrl`belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde, erişim belirteci aşağıdaki seçenekler `accessTokenFactory` nesnesi üzerinde `withUrl`alan ayarlayarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java istemcisinde, [HttpHubConnectionBuilder'a](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)erişim belirteç fabrikası sağlayarak kimlik doğrulama için kullanılacak bir taşıyıcı belirteci yapılandırabilirsiniz. [Bir RxJava](https://github.com/ReactiveX/RxJava) [\<Tek String>](https://reactivex.io/documentation/single.html)sağlamak için [AccessTokenFactory ile](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Single.defer'a](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)yapılan bir çağrıyla, istemciniz için erişim belirteçleri üretmek için mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımlarını ve canlı tutma seçeneklerini yapılandırma

Zaman ayarı yapılandırmak ve canlı tutma davranışını `HubConnection` yapılandırmak için ek seçenekler nesnenin kendisinde kullanılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `Closed` olayı tetikler (JavaScript'te).`onclose` Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `Closed` eder`onclose` ve olayı (JavaScript'te) tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |

.NET İstemci'sinde zaman akçe değerleri değerler olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onclose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman arası. Sunucu bu aralıkta bir ileti göndermediyse, istemci sunucunun bağlantısının kesildiğini düşünür ve `onClose` olayı tetikler. Bu değer, sunucudan gönderilen **ve** zaman aralığı içinde istemci tarafından alınan bir ping iletisi için yeterince büyük olmalıdır. Önerilen değer, ping'lerin gelmesi için `KeepAliveInterval` zaman tanımak için sunucunun değerinin en az iki katı olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman ara. Sunucu bu aralıkta el sıkışma yanıtı göndermezse, istemci el sıkışmayı iptal `onClose` eder ve olayı tetikler. Bu, yalnızca ağır ağ gecikmesi nedeniyle el sıkışma zaman sonu hataları oluşuyorsa değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla ayrıntı için [SignalR Hub Protokol Belirtimi'ne](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)bakın. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Ek seçenekler Java istemcisinde `WithUrl` `withUrl` çeşitli yapılandırma API'leri üzerinde `HubConnectionBuilder` (JavaScript) `HttpHubConnectionBuilder` yönteminde veya üzerinde yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET Seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `ClientCertificates` | Boş | İstekleri doğrulamak için gönderilecek TLS sertifikaları koleksiyonu. |
| `Cookies` | Boş | Her HTTP isteğiyle gönderilecek http çerezleri koleksiyonu. |
| `Credentials` | Boş | Her HTTP isteğiyle gönderilecek kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. İstemcinin kapatma isteğini kabul etmesi için sunucuyu kapattıktan sonra beklediği maksimum süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |
| `HttpMessageHandlerFactory` | `null` | HTTP isteklerini göndermek için kullanılanı `HttpMessageHandler` yapılandırmak veya değiştirmek için kullanılabilecek bir temsilci. WebSocket bağlantıları için kullanılmaz. Bu temsilci nin null olmayan bir değer döndürmesi gerekir ve varsayılan değeri parametre olarak alır. Bu varsayılan değerdeki ayarları değiştirin ve döndürün veya yeni `HttpMessageHandler` bir örneği döndürün. **İşleyiciyi değiştirirken, sağlanan işleyiciden saklamak istediğiniz ayarları kopyaladığından emin olun, aksi takdirde yapılandırılan seçenekler (Çerezler ve Üstbilgi gibi) yeni işleyici için geçerli olmayacaktır.** |
| `Proxy` | `null` | HTTP isteklerini gönderirken kullanılacak bir HTTP proxy'si. |
| `UseDefaultCredentials` | `false` | BU boolean'ı HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını sağlar. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilecek bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) bir örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript Seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java Seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde Taşıyıcı kimlik doğrulama belirteci olarak sağlanan bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Bunu, `true` anlaşma adımını atlamak için ayarlayın. **Yalnızca WebSockets aktarımı yalnızca etkin aktarım olduğunda desteklenir.** Azure SinyalR Hizmeti'ni kullanırken bu ayar etkinleştirilemez. |
| `withHeader` `withHeaders` | Boş | Her HTTP isteği ile göndermek için ek HTTP başlıkları Bir Harita. |

---

.NET İstemci'sinde, bu seçenekler aşağıdakilere `WithUrl`sağlanan seçenekler temsilcisi tarafından değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript İstemcisinde, bu seçenekler aşağıdakilere sağlanan `withUrl`bir JavaScript nesnesinde sağlanabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler `HttpHubConnectionBuilder` iade edilen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
