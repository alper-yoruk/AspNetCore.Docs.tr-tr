---
title: ASP.NET Core SignalR yapılandırması
author: bradygaster
description: ASP.NET Core SignalR uygulamalarını nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559004"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR yapılandırması

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html). Her protokol serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir. `AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir. Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır. Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir. Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).

Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur. Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:

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
> JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft. JSON öğesine geç

' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir. Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder. Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer Double `KeepAliveInterval` değeridir.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir. Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir. |
| `EnableDetailedErrors` | `false` | İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir. |
| `StreamBufferCapacity` | `10` | İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı. Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.|
| `MaximumReceiveMessageSize` | 32 KB | Tek bir gelen hub iletisinin en büyük boyutu. |

Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.

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

Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.

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

Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir. |
| `AuthorizationData` | Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır. | Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir. |
| `Transports` | Tüm aktarımlar etkin. | Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması. |
| `LongPolling` | Aşağıya bakın. | Uzun yoklama taşımasına özgü ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets taşımasına özgü ek seçenekler. |
| `MinimumProtocolVersion` | 0 | Negotiate protokolünün en düşük sürümünü belirtin. Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır. |

Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre. Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur. |

WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir. Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.

> [!NOTE]
> Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz. Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz. `AddConsole` Uzantı yöntemini çağırın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır. Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin. Günlükler tarayıcı konsolu penceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz. Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir. Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler. Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.

| Dize                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ya da**`information` | `LogLevel.Information` |
| `warn`**ya da**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).

SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir. Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir. Tüm aktarımlar varsayılan olarak etkindir.

Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.

Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder` Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın. .NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`). JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır. Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.

.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

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

SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın

Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `ClientCertificates` | Olmamalıdır | Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu. |
| `Cookies` | Olmamalıdır | Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu. |
| `Credentials` | Olmamalıdır | Her HTTP isteğiyle gönderilen kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |
| `HttpMessageHandlerFactory` | `null` | HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci. WebSocket bağlantıları için kullanılmıyor. Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır. Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün. **İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.** |
| `Proxy` | `null` | HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si. |
| `UseDefaultCredentials` | `false` | Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını mümkün. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withCredentials` | `true` | CORS isteğiyle kimlik bilgilerinin gönderilip gönderilmeyeceğini belirtir. Azure App Service, yapışkan oturumlar için tanımlama bilgilerini kullanır ve bu seçeneğin doğru şekilde çalışmasını gerektirir. SignalR ile CORS hakkında daha fazla bilgi için bkz <xref:signalr/security#cross-origin-resource-sharing>.. |

# <a name="java"></a>[Java](#tab/java)

| Java seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withHeader` `withHeaders` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |

---

.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

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
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html). Her protokol serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir. `AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir. Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır. Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir. Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).

Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur. Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:

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
> JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft. JSON öğesine geç

' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir. Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder. Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer Double `KeepAliveInterval` değeridir.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir. Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir. |
| `EnableDetailedErrors` | `false` | İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir. |
| `StreamBufferCapacity` | `10` | İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı. Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.|
| `MaximumReceiveMessageSize` | 32 KB | Tek bir gelen hub iletisinin en büyük boyutu. |

Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.

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

Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.

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

Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir. |
| `AuthorizationData` | Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır. | Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir. |
| `Transports` | Tüm aktarımlar etkin. | Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması. |
| `LongPolling` | Aşağıya bakın. | Uzun yoklama taşımasına özgü ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets taşımasına özgü ek seçenekler. |
| `MinimumProtocolVersion` | 0 | Negotiate protokolünün en düşük sürümünü belirtin. Bu, istemcileri daha yeni sürümlerle sınırlandırmak için kullanılır. |

Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre. Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur. |

WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir. Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.

> [!NOTE]
> Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz. Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz. `AddConsole` Uzantı yöntemini çağırın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır. Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin. Günlükler tarayıcı konsolu penceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz. Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir. Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler. Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.

| Dize                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ya da**`information` | `LogLevel.Information` |
| `warn`**ya da**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).

SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir. Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir. Tüm aktarımlar varsayılan olarak etkindir.

Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.

Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder` Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın. .NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`). JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır. Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.

.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

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

SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın

Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `ClientCertificates` | Olmamalıdır | Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu. |
| `Cookies` | Olmamalıdır | Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu. |
| `Credentials` | Olmamalıdır | Her HTTP isteğiyle gönderilen kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |
| `HttpMessageHandlerFactory` | `null` | HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci. WebSocket bağlantıları için kullanılmıyor. Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır. Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün. **İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.** |
| `Proxy` | `null` | HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si. |
| `UseDefaultCredentials` | `false` | Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını mümkün. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withHeader` `withHeaders` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |

---

.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

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
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack serileştirme seçenekleri

ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html). Her protokol serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir. `AddJsonProtocol`, içinde `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilir. Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır. Bu nesnedeki [payloadserializeroptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) özelliği, bağımsız değişkenlerin serileştirmesini ve dönüş değerlerini yapılandırmak için kullanılabilecek bir `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> nesnedir. Daha fazla bilgi için bkz. [System. Text. JSON belgeleri](/dotnet/api/system.text.json).

Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine özellik adlarının büyük küçük harflerini değiştirmemelidir şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur. Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:

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
> JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft. JSON öğesine geç

' De `Newtonsoft.Json` `System.Text.Json`desteklenmeyen özelliklerine ihtiyacınız varsa, bkz. [Newtonsoft. JSON öğesine geçme](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir. Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder. Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer Double `KeepAliveInterval` değeridir.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir. Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir. |
| `EnableDetailedErrors` | `false` | İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir. |
| `StreamBufferCapacity` | `10` | İstemci yükleme akışları için ara belleğe oluşturulabilecek en fazla öğe sayısı. Bu sınıra ulaşıldığında, sunucu akış öğelerini işlemeden, etkinleştirmeleri işleme engellenir.|
| `MaximumReceiveMessageSize` | 32 KB | Tek bir gelen hub iletisinin en büyük boyutu. |

Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.

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

Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) bir temsilci geçirilerek yapılandırılır.

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

Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | İstemci tarafından, geri basıncı uygulamadan önce sunucunun arabelleğe aldığı en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç uygulamadan daha büyük iletileri daha hızlı almasına izin verir, ancak bellek tüketimini artırabilir. |
| `AuthorizationData` | Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır. | Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Uygulama tarafından, geri basıncını gözlemlenmadan önce sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı. Bu değeri artırmak, sunucunun geri basınç beklemeden daha büyük iletileri daha hızlı arabelleğe almasına izin verir, ancak bellek tüketimini artırabilir. |
| `Transports` | Tüm aktarımlar etkin. | Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması. |
| `LongPolling` | Aşağıya bakın. | Uzun yoklama taşımasına özgü ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets taşımasına özgü ek seçenekler. |

Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre. Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur. |

WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir. Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.

> [!NOTE]
> Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz. Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz. `AddConsole` Uzantı yöntemini çağırın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır. Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin. Günlükler tarayıcı konsolu penceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Bir `LogLevel` değer yerine, bir günlük düzeyi adını temsil eden `string` bir değer de sağlayabilirsiniz. Bu, `LogLevel` sabitlere erişiminizin olmadığı ortamlarda SignalR günlüğünü yapılandırırken kullanışlıdır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Aşağıdaki tabloda kullanılabilir günlük düzeyleri listelenmektedir. Sağladığınız değer, günlüğe kaydedilecek `configureLogging` **Minimum** günlük düzeyini belirler. Bu düzeyde günlüğe kaydedilen iletiler **veya tabloda bundan sonra listelenen düzeyler**günlüğe kaydedilir.

| Dize                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**ya da**`information` | `LogLevel.Information` |
| `warn`**ya da**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).

SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir. Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir. Tüm aktarımlar varsayılan olarak etkindir.

Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.

Java istemcisinde, taşıma `withTransport` yöntemi ile birlikte seçilir. `HttpHubConnectionBuilder` Java istemcisi WebSockets taşımasını varsayılan olarak kullanmaktır.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java istemcisi henüz taşıma geri dönüşü desteklemez.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın. .NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`). JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır. Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.

.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

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

SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın

Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `ClientCertificates` | Olmamalıdır | Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu. |
| `Cookies` | Olmamalıdır | Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu. |
| `Credentials` | Olmamalıdır | Her HTTP isteğiyle gönderilen kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |
| `HttpMessageHandlerFactory` | `null` | HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci. WebSocket bağlantıları için kullanılmıyor. Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır. Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün. **İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.** |
| `Proxy` | `null` | HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si. |
| `UseDefaultCredentials` | `false` | Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını mümkün. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withHeader` `withHeaders` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |

---

.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

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

ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html). Her protokol serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, yöntekinizdeki `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir. Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır. Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, bağımsız değişkenlerin ve dönüş `JsonSerializerSettings` değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir. Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.
 
Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur. Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:

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
> JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir. Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 saniye | Sunucu, bu aralıkta (canlı tut dahil) bir ileti almamışsa istemcinin bağlantısının kesileceğini kabul eder. Bu işlem, uygulanması nedeniyle istemcinin bağlantısının gerçekten kesilmesinin ardından bu zaman aşımı aralığından daha uzun sürebilir. Önerilen değer Double `KeepAliveInterval` değeridir.|
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir. Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir. |
| `EnableDetailedErrors` | `false` | İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir. |

Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.

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

Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek yapılandırılır.

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

Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. |
| `AuthorizationData` | Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır. | Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir. |
| `Transports` | Tüm aktarımlar etkin. | Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması. |
| `LongPolling` | Aşağıya bakın. | Uzun yoklama taşımasına özgü ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets taşımasına özgü ek seçenekler. |

Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre. Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur. |

WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir. Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.

> [!NOTE]
> Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz. Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz. `AddConsole` Uzantı yöntemini çağırın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır. Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin. Günlükler tarayıcı konsolu penceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).

SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir. Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir. Tüm aktarımlar varsayılan olarak etkindir.

Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

Java Client WebSockets 'in bu sürümünde, kullanılabilir tek aktarım bir sürümdür.

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın. .NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`). JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır. Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.

.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

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

SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın

Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `keepAliveIntervalInMilliseconds` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 saniye (15.000 milisaniye) | İstemcinin ping iletileri gönderdiği aralığı belirler. İstemciden herhangi bir ileti gönderildiğinde, süreölçeri aralığın başına sıfırlanır. İstemci, sunucuda `ClientTimeoutInterval` küme içinde bir ileti göndermediyse sunucu, istemcinin bağlantısının kesileceğini kabul eder. |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `ClientCertificates` | Olmamalıdır | Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu. |
| `Cookies` | Olmamalıdır | Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu. |
| `Credentials` | Olmamalıdır | Her HTTP isteğiyle gönderilen kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |
| `HttpMessageHandlerFactory` | `null` | HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci. WebSocket bağlantıları için kullanılmıyor. Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır. Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün. **İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.** |
| `Proxy` | `null` | HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si. |
| `UseDefaultCredentials` | `false` | Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını mümkün. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withHeader` `withHeaders` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |

---

.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

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

ASP.NET Core SignalR, kodlama iletileri için iki protokolü destekler: [JSON](https://www.json.org/) ve [MessagePack](https://msgpack.org/index.html). Her protokol serileştirme yapılandırma seçenekleri vardır.

JSON serileştirme, yöntekinizdeki `Startup.ConfigureServices` [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) öğesinden sonra eklenebilen [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) genişletme yöntemi kullanılarak sunucuda yapılandırılabilir. Yöntemi `AddJsonProtocol` , bir `options` nesneyi alan bir temsilciyi alır. Bu nesnedeki [Payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) özelliği, bağımsız değişkenlerin ve dönüş `JsonSerializerSettings` değerlerinin serileştirilmesi yapılandırmak için kullanılabilen bir JSON.net nesnesidir. Daha fazla bilgi için [JSON.net belgelerine](https://www.newtonsoft.com/json/help/html/Introduction.htm)bakın.
 
Örnek olarak, serileştiriciyi varsayılan "camelCase" adları yerine "PascalCase" özellik adlarını kullanacak şekilde yapılandırmak için aşağıdaki kodu kullanın `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET istemcisinde, [Hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)'da `AddJsonProtocol` aynı genişletme yöntemi bulunur. Uzantı `Microsoft.Extensions.DependencyInjection` metodunu çözümlemek için ad alanı içeri aktarılmalıdır:

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
> JavaScript istemcisinde Şu anda JSON serileştirme yapılandırmak mümkün değildir.

### <a name="messagepack-serialization-options"></a>MessagePack serileştirme seçenekleri

MessagePack serileştirme, [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) çağrısına bir temsilci sağlanarak yapılandırılabilir. Daha fazla ayrıntı için bkz. [SignalR 'de MessagePack](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Şu anda JavaScript istemcisinde MessagePack serileştirme yapılandırmak mümkün değildir.

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Aşağıdaki tabloda, SignalR hub 'larını yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 saniye | İstemci bu zaman aralığı içinde bir ilk el sıkışma iletisi göndermezse bağlantı kapatılır. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 saniye | Sunucu bu Aralık dahilinde bir ileti göndermediyse bağlantının açık tutulması için bir ping iletisi otomatik olarak gönderilir. Değişiklik yaparken `KeepAliveInterval`, istemcideki `ServerTimeout` / `serverTimeoutInMilliseconds` ayarı değiştirin. `ServerTimeout` / Önerilen `serverTimeoutInMilliseconds` değer Double `KeepAliveInterval` değeridir.  |
| `SupportedProtocols` | Tüm yüklü protokoller | Bu hub tarafından desteklenen protokoller. Varsayılan olarak, sunucuda kayıtlı tüm protokollere izin verilir, ancak tek tek hub 'lara yönelik belirli protokolleri devre dışı bırakmak için protokollerin bu listeden kaldırılması gerekir. |
| `EnableDetailedErrors` | `false` | İse `true`, bir hub yönteminde özel durum oluştuğunda istemcilere ayrıntılı özel durum iletileri döndürülür. Varsayılan `false`olarak, bu özel durum iletileri hassas bilgiler içerebilir. |

Seçenekler, `AddSignalR` içindeki `Startup.ConfigureServices`çağrıya bir seçenek temsilcisi sağlayarak tüm Hub 'lar için yapılandırılabilir.

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

Tek bir hub için Seçenekler ' de `AddSignalR` belirtilen genel seçenekleri geçersiz kılar ve kullanılarak <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>yapılandırılabilir:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Gelişmiş HTTP yapılandırma seçenekleri

Aktarımlar `HttpConnectionDispatcherOptions` ve bellek arabellek yönetimiyle ilgili gelişmiş ayarları yapılandırmak için kullanın. Bu seçenekler, ' de `Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) bir temsilci geçirilerek yapılandırılır.

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

Aşağıdaki tabloda ASP.NET Core SignalR 'nin gelişmiş HTTP seçeneklerini yapılandırma seçenekleri açıklanmaktadır:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | İstemciden sunucunun arabelleğe aldığı en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler almasına izin verir, ancak bellek tüketimini olumsuz etkileyebilir. |
| `AuthorizationData` | Veriler, hub sınıfına uygulanan `Authorize` özniteliklerden otomatik olarak toplanır. | Bir istemcinin hub 'a bağlanmasına yetkili olup olmadığını belirlemede kullanılan [ıauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) nesnelerinin listesi. |
| `TransportMaxBufferSize` | 32 KB | Uygulama tarafından sunucunun arabelleklerinin gönderdiği en fazla bayt sayısı. Bu değeri artırmak, sunucunun daha büyük iletiler göndermesini sağlar, ancak bellek tüketimini olumsuz etkileyebilir. |
| `Transports` | Tüm aktarımlar etkin. | Bir istemcinin bağlanmak için kullanabileceği `HttpTransportType` taşımaları kısıtlayabilecek bir bit bayrakları değeri numaralandırması. |
| `LongPolling` | Aşağıya bakın. | Uzun yoklama taşımasına özgü ek seçenekler. |
| `WebSockets` | Aşağıya bakın. | WebSockets taşımasına özgü ek seçenekler. |

Uzun yoklama taşıması, `LongPolling` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 saniye | Tek bir yoklama isteğini sonlandırmadan önce sunucunun istemciye göndermek için bekleyeceği en uzun süre. Bu değeri azaltmak istemcinin yeni yoklama istekleri daha sık vermesine neden olur. |

WebSocket taşıması, `WebSockets` özelliği kullanılarak yapılandırılabilecek ek seçeneklere sahiptir:

| Seçenek | Varsayılan Değer | Açıklama |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 saniye | Sunucu kapandıktan sonra, istemci bu zaman aralığında kapanamazsa bağlantı sonlandırılır. |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol` Üstbilgiyi özel bir değere ayarlamak için kullanılabilen bir temsilci. Temsilci, istemci tarafından istenen değerleri girdi olarak alır ve istenen değeri döndürmesi beklenir. |

## <a name="configure-client-options"></a>İstemci seçeneklerini yapılandırma

İstemci seçenekleri `HubConnectionBuilder` tür üzerinde yapılandırılabilir (.net ve JavaScript istemcilerinde kullanılabilir). Java istemcisinde de bulunur, ancak `HttpHubConnectionBuilder` alt sınıf, Oluşturucu yapılandırma seçeneklerinin yanı sıra `HubConnection` kendisi de içerir.

### <a name="configure-logging"></a>Günlüğe kaydetmeyi yapılandırma

Günlüğe kaydetme, .NET Istemcisinde `ConfigureLogging` yöntemi kullanılarak yapılandırılır. Günlüğe kaydetme sağlayıcıları ve filtreler, sunucuda oldukları gibi aynı şekilde kaydedilebilir. Daha fazla bilgi için [oturum açma ASP.NET Core](xref:fundamentals/logging/index) belgelerine bakın.

> [!NOTE]
> Günlüğe kaydetme sağlayıcılarını kaydetmek için gerekli paketleri yüklemelisiniz. Tam liste için docs 'ın [yerleşik günlük sağlayıcıları](xref:fundamentals/logging/index#built-in-logging-providers) bölümüne bakın.

Örneğin, konsol günlüğünü etkinleştirmek için `Microsoft.Extensions.Logging.Console` NuGet paketini yüklersiniz. `AddConsole` Uzantı yöntemini çağırın:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript istemcisinde, benzer `configureLogging` bir yöntem vardır. Üretilecek günlük `LogLevel` iletilerinin en düşük düzeyini belirten bir değer girin. Günlükler tarayıcı konsolu penceresine yazılır.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Günlüğe kaydetmeyi tamamen devre dışı bırakmak `signalR.LogLevel.None` için `configureLogging` yönteminde belirtin.

Günlüğe kaydetme hakkında daha fazla bilgi için bkz. [SignalR Diagnostics belgeleri](xref:signalr/diagnostics).

SignalR Java istemcisi, günlük kaydı için [dolayısıyla slf4j](https://www.slf4j.org/) kitaplığını kullanır. Bu, kitaplık kullanıcılarının belirli bir günlüğe kaydetme bağımlılığı vererek kendi belirli günlük uygulamasını seçmesine olanak sağlayan, üst düzey bir günlüğe kaydetme API 'sidir. Aşağıdaki kod parçacığı, SignalR Java istemcisiyle `java.util.logging` nasıl kullanılacağını göstermektedir.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Bağımlılıklarınız için günlük kaydını yapılandırmazsanız, DOLAYıSıYLA SLF4J aşağıdaki uyarı iletisiyle varsayılan işlem olmayan bir günlükçü yükler:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Bu, güvenle yoksayılabilir.

### <a name="configure-allowed-transports"></a>İzin verilen taşımaları yapılandırma

SignalR tarafından kullanılan aktarımlar `WithUrl` çağrıda (`withUrl` JavaScript 'te) yapılandırılabilir. Bir bit düzeyinde OR değeri `HttpTransportType` , istemciyi yalnızca belirtilen aktarımları kullanacak şekilde kısıtlamak için kullanılabilir. Tüm aktarımlar varsayılan olarak etkindir.

Örneğin, sunucu tarafından gönderilen olay taşımasını devre dışı bırakmak, ancak WebSockets ve uzun yoklama bağlantılarına izin vermek için:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript istemcisinde aktarımlar, için `transport` `withUrl`belirtilen seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Taşıyıcı kimlik doğrulamasını yapılandırma

Kimlik doğrulama verilerini SignalR istekleriyle birlikte sağlamak için, istenen erişim `AccessTokenProvider` belirtecini döndüren`accessTokenFactory` bir Işlevi belirtmek için (JavaScript 'te) seçeneğini kullanın. .NET Istemcisinde, bu erişim belirteci HTTP "taşıyıcı kimlik doğrulaması" belirteci olarak geçirilir (bir türü olan `Authorization` üst bilgi kullanılarak `Bearer`). JavaScript istemcisinde, tarayıcı API 'Lerinin üstbilgileri uygulama özelliğini (özellikle de sunucu tarafından gönderilen olaylar ve WebSockets istekleri) kısıtlayacağı birkaç durum **dışında** , erişim belirteci bir taşıyıcı belirteci olarak kullanılır. Bu durumlarda, erişim belirteci bir sorgu dizesi değeri `access_token`olarak sağlanır.

.NET istemcisinde `AccessTokenProvider` seçeneği, içindeki `WithUrl`seçenekler temsilcisi kullanılarak belirtilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript istemcisinde erişim belirteci, içindeki `accessTokenFactory` `withUrl`seçenekler nesnesindeki alanı ayarlanarak yapılandırılır:

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

SignalR Java istemcisinde, [Httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)'a bir erişim belirteci fabrikası sağlayarak kimlik doğrulaması için kullanılacak bir taşıyıcı belirteç yapılandırabilirsiniz. [Rxjava](https://github.com/ReactiveX/RxJava) [tek\<dize>](https://reactivex.io/documentation/single.html)sağlamak için [withaccesstokenfactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) kullanın. [Tek. ertele](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)çağrısıyla, istemciniz için erişim belirteçleri oluşturmak üzere mantık yazabilirsiniz.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Zaman aşımını yapılandırın ve canlı tut seçeneklerini yapılandırın

Zaman aşımını ve canlı tutma davranışını yapılandırmaya yönelik ek seçenekler `HubConnection` nesnenin kendisinde bulunabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |
| `HandshakeTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `Closed` olayı tetikler (`onclose` JavaScript 'te). Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

.NET Istemcisinde zaman aşımı değerleri değer olarak `TimeSpan` belirtilir.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onclose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping için en az iki sunucu `KeepAliveInterval` değeri olan bir sayıdır. |

# <a name="java"></a>[Java](#tab/java)

| Seçenek | Varsayılan değer | Açıklama |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 saniye (30.000 milisaniye) | Sunucu etkinliği için zaman aşımı. Sunucu bu aralıkta bir ileti göndermediyse istemci, sunucunun bağlantısını kesti ve `onClose` olayı tetikler. Bu değer, ping iletisinin sunucudan gönderilmesi **ve** istemci tarafından zaman aşımı aralığı içinde alınması için yeterince büyük olmalıdır. Önerilen değer, ping bir sürenin gelmesi için en az iki `KeepAliveInterval` değerin değeri olan bir sayıdır. |
| `withHandshakeResponseTimeout` | 15 saniye | İlk sunucu el sıkışması için zaman aşımı. Sunucu bu aralıkta bir el sıkışma yanıtı göndermezse, istemci el sıkışmasını iptal eder ve `onClose` olayı tetikler. Bu, yalnızca önemli ağ gecikmesi nedeniyle el sıkışma zaman aşımı hataları gerçekleşirken değiştirilmesi gereken gelişmiş bir ayardır. El sıkışma işlemi hakkında daha fazla bilgi için bkz. [SignalR hub protokol belirtimi](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Ek seçenekleri yapılandırma

Java istemcisinde `WithUrl` `HttpHubConnectionBuilder` içindeki çeşitli yapılandırma API 'lerinde `HubConnectionBuilder` veya`withUrl` üzerinde bulunan (JavaScript 'te) yönteminde ek seçenekler yapılandırılabilir:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET seçeneği |  Varsayılan değer | Açıklama |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `SkipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `ClientCertificates` | Olmamalıdır | Kimlik doğrulaması isteklerine gönderilmek üzere TLS sertifikaları koleksiyonu. |
| `Cookies` | Olmamalıdır | Her HTTP isteğiyle gönderilmek üzere HTTP tanımlama bilgilerinin bir koleksiyonu. |
| `Credentials` | Olmamalıdır | Her HTTP isteğiyle gönderilen kimlik bilgileri. |
| `CloseTimeout` | 5 saniye | Yalnızca WebSockets. Sunucunun kapatma isteğini onaylaması için kapatıldıktan sonra bekleyeceği en uzun süre. Sunucu bu süre içinde kapatmayı kabul etmezse, istemci bağlantısını keser. |
| `Headers` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |
| `HttpMessageHandlerFactory` | `null` | HTTP istekleri göndermek için `HttpMessageHandler` kullanılan yapılandırmak veya değiştirmek için kullanılan bir temsilci. WebSocket bağlantıları için kullanılmıyor. Bu temsilci null olmayan bir değer döndürmelidir ve varsayılan değeri bir parametre olarak alır. Bu varsayılan değerde ayarları değiştirin ve döndürün ya da yeni `HttpMessageHandler` bir örnek döndürün. **İşleyiciyi değiştirirken, belirtilen işleyiciden tutmak istediğiniz ayarları kopyalamadığınızdan emin olun, aksi takdirde, yapılandırılan seçenekler (tanımlama bilgileri ve üstbilgiler gibi) yeni işleyiciye uygulanmaz.** |
| `Proxy` | `null` | HTTP istekleri gönderilirken kullanılacak bir HTTP proxy 'si. |
| `UseDefaultCredentials` | `false` | Bu Boole değeri HTTP ve WebSockets istekleri için varsayılan kimlik bilgilerini gönderecek şekilde ayarlayın. Bu, Windows kimlik doğrulamasının kullanılmasını mümkün. |
| `WebSocketConfiguration` | `null` | Ek WebSocket seçeneklerini yapılandırmak için kullanılabilen bir temsilci. Seçenekleri yapılandırmak için kullanılabilecek [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) örneğini alır. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript seçeneği | Varsayılan Değer | Açıklama |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `skipNegotiation` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |

# <a name="java"></a>[Java](#tab/java)

| Java seçeneği | Varsayılan Değer | Açıklama |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP isteklerinde taşıyıcı kimlik doğrulama belirteci olarak belirtilen bir dize döndüren bir işlev. |
| `shouldSkipNegotiate` | `false` | Anlaşma adımını atlamak `true` için bunu olarak ayarlayın. **Yalnızca WebSockets taşıması etkin olan tek taşıma olduğunda desteklenir**. Bu ayar, Azure SignalR hizmeti kullanılırken etkinleştirilemez. |
| `withHeader` `withHeaders` | Olmamalıdır | Her HTTP isteğiyle birlikte gönderilmek üzere ek HTTP üstbilgileri haritası. |

---

.NET Istemcisinde bu seçenekler, aşağıdakileri yapmak için `WithUrl`belirtilen seçenekler temsilcisinden değiştirilebilir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript Istemcisinde, bu seçenekler şu şekilde `withUrl`sağlanmış bir JavaScript nesnesi içinde bulunabilir:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java istemcisinde, bu seçenekler, içindeki `HttpHubConnectionBuilder` döndürülen yöntemlerle yapılandırılabilir`HubConnectionBuilder.create("HUB URL")`

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
