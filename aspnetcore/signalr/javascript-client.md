---
title: SignalRJavaScript istemcisi ASP.NET Core
author: bradygaster
description: JavaScript istemcisine ASP.NET Core genel bakış SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: e6feeb0009034d9ea92f09c44ed0ca882d80fe1b
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504768"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a>SignalRJavaScript istemcisi ASP.NET Core

, [Oychel Appel](https://twitter.com/rachelappel) tarafından

SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-client-package"></a>SignalRİstemci paketini yükler

SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır. Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.

### <a name="install-with-npm"></a>NPM ile yüklensin

Visual Studio kullanıyorsanız, kök klasörde **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın. Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

NPM *node_modules \\ @microsoft\signalr\dist\browser * klasöre paket içeriğini yüklüyor. *Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun. *signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

NPM *node_modules \\ @aspnet\signalr\dist\browser * klasöre paket içeriğini yüklüyor. *Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun. *signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.

::: moniker-end

SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` . Örneğin:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Content Delivery Network kullanma (CDN)

İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun. Örneğin:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:

::: moniker range=">= aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/microsoft-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [cdnjs](https://cdnjs.com/libraries/aspnet-signalr)
* [jsDelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a>LibMan ile Install

[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir. Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin. Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Bir hub 'a bağlanma

Aşağıdaki kod bir bağlantı oluşturur ve başlatır. Hub 'ın adı büyük/küçük harfe duyarlıdır.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a>Çapraz kaynak bağlantıları

Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler. Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.

Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır. Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>İstemciden çağrı merkezi yöntemleri

JavaScript istemcileri, [Hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)'un [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır. `invoke`Yöntemi iki bağımsız değişkeni kabul eder:

* Hub yönteminin adı. Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .
* Hub yönteminde tanımlanan bağımsız değişkenler. Aşağıdaki örnekte, bağımsız değişken adı `message` . Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir. Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür. , `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir. Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir. `then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .

`send`Yöntemi bir JavaScript döndürür `Promise` . `Promise`İleti sunucuya gönderildiğinde çözümlenir. İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir. `then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .

> [!NOTE]
> Kullanmak `send` Sunucu iletiyi almaa kadar beklemez. Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.

## <a name="call-client-methods-from-hub"></a>Hub 'dan istemci yöntemlerini çağır

Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40aspnet/signalr/hubconnection#on) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .

* JavaScript istemci yönteminin adı. Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .
* Hub 'ın yönteme geçirdiği bağımsız değişkenler. Aşağıdaki örnekte, bağımsız değişken değeri `message` .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Önceki kod, `connection.on` sunucu tarafı kodu [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemi kullanılarak çağırdığında çalıştırılır.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .

> [!NOTE]
> En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` . Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.

## <a name="error-handling-and-logging"></a>Hata işleme ve günlüğe kaydetme

`catch` `start` İstemci tarafı hatalarını işlemek için yönteminin sonuna bir yöntemi zincirleyin. `console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın. İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir. Kullanılabilir günlük düzeyleri aşağıdaki gibidir:

* `signalR.LogLevel.Error`: Hata iletileri. `Error`Yalnızca iletileri günlüğe kaydeder.
* `signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri. Günlükler `Warning` ve `Error` mesajlar.
* `signalR.LogLevel.Information`: Hata olmayan durum iletileri. Günlükler `Information` , `Warning` ve `Error` iletileri.
* `signalR.LogLevel.Trace`: Trace iletileri. Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.

Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın. İletiler tarayıcı konsoluna kaydedilir.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>İstemcileri yeniden bağla

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Otomatik olarak yeniden bağlan

İçin JavaScript istemcisi, SignalR `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir. Varsayılan olarak otomatik olarak yeniden bağlanmaz.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Herhangi bir parametre olmadan, `withAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.

Yeniden bağlanma girişimlerini başlatmadan önce, durumuna `HubConnection` geçiş yapmak `HubConnectionState.Reconnecting` yerine geri çağırmaları harekete geçer ve `onreconnecting` `Disconnected` `onclose` `HubConnection` Otomatik yeniden bağlanma yapılandırması olmadan geri çağırmaları tetikleyerek. Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir. Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.

Bağlantı sunucuya tamamen yeni göründüğünden geri aramaya yeni bir verilecek `connectionId` `onreconnected` .

> [!WARNING]
> `onreconnected`Geri aramanın `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur.

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` , `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir. Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `withAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır. Bu, varsayılan yapılandırma için de geçerlidir.

İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.

İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.

Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.

Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `nextRetryDelayInMilliseconds` .

`nextRetryDelayInMilliseconds` türünde tek bir bağımsız değişken alır `RetryContext` . ,, `RetryContext` `previousRetryCount` `elapsedMilliseconds` Ve `retryReason` sırasıyla bir olan `number` `number` üç özelliğe `Error` sahiptir:. İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve `retryReason` bağlantının kaybolmasına neden olan hata olacaktır. Her başarısız yeniden deneme denemesinden sonra `previousRetryCount` bir artırılır `elapsedMilliseconds` . Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve `retryReason` son yeniden bağlanma girişiminin başarısız olmasına neden olan hata olur.

`nextRetryDelayInMilliseconds` sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.

::: moniker-end

### <a name="manually-reconnect"></a>El ile yeniden bağlan

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3,0 ' den önce, için JavaScript istemcisi SignalR otomatik olarak yeniden bağlanmaz. İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.

::: moniker-end

Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:

1. Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.
1. `start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.

## <a name="additional-resources"></a>Ek kaynaklar

* [JavaScript API'si başvurusu](/javascript/api/?view=signalr-js-latest)
* [JavaScript öğreticisi](xref:tutorials/signalr)
* [WebPack ve TypeScript öğreticisi](xref:tutorials/signalr-typescript-webpack)
* [Merkezler](xref:signalr/hubs)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Çıkış noktaları arası Istekler (CORS)](xref:security/cors)
* [Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)
