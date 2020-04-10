---
title: ASP.NET SignalR Core JavaScript istemcisi
author: bradygaster
description: Core SignalR JavaScript istemcisi ASP.NET genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994578"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET SignalR Core JavaScript istemcisi

Yazar: [Rachel Appel](https://twitter.com/rachelappel)

ASP.NET Core SignalR JavaScript istemci kitaplığı, geliştiricilerin sunucu tarafındaki hub kodunu aramasını sağlar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>İstemci paketini SignalR yükleme

SignalR JavaScript istemci kitaplığı [npm](https://www.npmjs.com/) paketi olarak teslim edilir. Aşağıdaki bölümler, istemci kitaplığını yüklemenin farklı yollarını sıralar.

### <a name="install-with-npm"></a>npm ile yükleyin

Visual Studio kullanıyorsanız, kök klasördeyken **Package Manager Console'dan** aşağıdaki komutları çalıştırın. Visual Studio Code **için, Entegre Terminal'den**aşağıdaki komutları çalıştırın.

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

npm paket içeriğini *node_modules\\ * klasörüne yükler. *wwwroot\\lib* klasörü altında *signalr* adlı yeni bir klasör oluşturun. *Signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

npm paket içeriğini *node_modules\\ * klasörüne yükler. *wwwroot\\lib* klasörü altında *signalr* adlı yeni bir klasör oluşturun. *Signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.

::: moniker-end

Öğedeki SignalR JavaScript istemcisi başvurun. `<script>` Örneğin:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>İçerik Dağıtım Ağı (CDN) kullanma

NPM ön koşulu olmadan istemci kitaplığını kullanmak için istemci kitaplığı CDN barındırılan bir kopyasına başvurun. Örneğin:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

İstemci kitaplığı aşağıdaki CDN'lerde kullanılabilir:

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

### <a name="install-with-libman"></a>LibMan ile yükleyin

[LibMan,](xref:client-side/libman/index) CDN tarafından barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir. Örneğin, yalnızca minified JavaScript dosyasını projeye ekleyin. Bu yaklaşımla ilgili ayrıntılar [ SignalR için](xref:tutorials/signalr#add-the-signalr-client-library)bkz.

## <a name="connect-to-a-hub"></a>Hub'a bağlanma

Aşağıdaki kod bir bağlantı oluşturur ve başlatır. Hub'ın adı büyük/küçük harf duyarsız.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Orijinler arası bağlantılar

Genellikle, tarayıcılar bağlantıları istenen sayfayla aynı etki alanından yükler. Ancak, başka bir etki alanına bağlantı gerektiği durumlar vardır.

Kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını önlemek [için, orijinler arası bağlantılar](xref:security/cors) varsayılan olarak devre dışı bırakılır. Bir çapraz kaynak isteğine izin vermek `Startup` için, bunu sınıfta etkinleştirin.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>İstemciden hub yöntemlerini arama

JavaScript istemcileri [HubConnection'ın](/javascript/api/%40aspnet/signalr/hubconnection) [çağırma](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi yle hub'larda genel yöntemleri çağırır. Yöntem `invoke` iki bağımsız değişkeni kabul eder:

* Hub yönteminin adı. Aşağıdaki örnekte, hub'daki yöntem `SendMessage`adı.
* Hub yönteminde tanımlanan bağımsız değişkenler. Aşağıdaki örnekte, bağımsız değişken `message`adı . Örnek kod, Internet Explorer dışındaki tüm büyük tarayıcıların geçerli sürümlerinde desteklenen ok işlevi sözdizimini kullanır.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Azure SignalR Hizmetini *Serverless modunda*kullanıyorsanız, istemciden hub yöntemlerini çağıramazsınız. Daha fazla bilgi için [ SignalR Hizmet belgelerine](/azure/azure-signalr/signalr-concept-serverless-development-config)bakın.

Yöntem `invoke` bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür. Sunucudaki `Promise` yöntem döndüğünde iade değeri (varsa) ile çözülür. Sunucudaki yöntem bir hata atarsa, `Promise` hata iletisi ile reddedilir. Bu `then` servis `catch` taleplerini `Promise` (veya `await` sözdizimi) işlemek için kendi üzerindeki yöntemleri kullanın.

Yöntem `send` bir JavaScript `Promise`döndürür. İleti `Promise` sunucuya gönderildiğinde çözülür. İletiyi gönderen bir hata varsa, hata iletisi ile `Promise` reddedilir. Bu `then` servis `catch` taleplerini `Promise` (veya `await` sözdizimi) işlemek için kendi üzerindeki yöntemleri kullanın.

> [!NOTE]
> Sunucu `send` iletiyi alana kadar kullanma beklemez. Sonuç olarak, sunucudan veri veya hata döndürmek mümkün değildir.

## <a name="call-client-methods-from-hub"></a>İstemci yöntemlerini hub'dan arama

Hub'dan ileti almak için, ['nin açık](/javascript/api/%40aspnet/signalr/hubconnection#on) yöntemini kullanarak bir yöntem `HubConnection`tanımlayın.

* JavaScript istemci yönteminin adı. Aşağıdaki örnekte, yöntem adı `ReceiveMessage`.
* Bağımsız değişkenler hub yönteme geçer. Aşağıdaki örnekte, bağımsız değişken `message`değeri .

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

Sunucu tarafındaki kod `connection.on` [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemini kullanarak aradığında, önceki kod çalışır.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalRtanımlanan yöntem adı ve bağımsız değişkenleri eşleştirerek `SendAsync` `connection.on`hangi istemci yöntemini arayacağını belirler.

> [!NOTE]
> En iyi uygulama olarak, `HubConnection` sonra `on` [başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini arayın. Bunu yapmak, işleyicilerinizin herhangi bir ileti alınmadan önce kaydedilmesini sağlar.

## <a name="error-handling-and-logging"></a>Hata işleme ve günlüğe kaydetme

İstemci tarafı hatalarını `start` işlemek için yöntemin sonuna bir `catch` yöntem zincirleyin. Tarayıcının konsolundaki hataları çıkarmak için kullanın. `console.error`

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Bağlantı yapıldığında günlüğe kaydetmek için bir logger ve olay türünü geçirerek istemci tarafı günlük izlemesini kurulum. İletiler belirtilen günlük düzeyi ve daha yüksek günlüğe kaydedilir. Kullanılabilir günlük düzeyleri aşağıdaki gibidir:

* `signalR.LogLevel.Error`&ndash; Hata iletileri. Yalnızca `Error` iletileri günlüğe kaydeder.
* `signalR.LogLevel.Warning`&ndash; Olası hatalar hakkında uyarı iletileri. Günlükler `Warning`ve `Error` mesajlar.
* `signalR.LogLevel.Information`&ndash; Durum iletileri hatasız. Günlükler `Information` `Warning`ve `Error` mesajlar.
* `signalR.LogLevel.Trace`&ndash; İletileri takip et. Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.

Günlük düzeyini [yapılandırmak](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) için [HubConnectionBuilder'da](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) yapılandırma Günlüğü yöntemini kullanın. İletiler tarayıcı konsoluna kaydedilir.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>İstemcileri yeniden bağlama

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Otomatik olarak yeniden bağlanma

Için JavaScript SignalR istemcisi `withAutomaticReconnect` [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)yöntemini kullanarak otomatik olarak yeniden bağlanmak için yapılandırılabilir. Varsayılan olarak otomatik olarak yeniden bağlanmaz.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Herhangi bir `withAutomaticReconnect()` parametre olmadan, istemciyi her yeniden bağlama denemesini denemeden önce sırasıyla 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırArak dört başarısız denemeden sonra durur.

Herhangi bir yeniden bağlanma `HubConnection` girişimine başlamadan `HubConnectionState.Reconnecting` önce, `onreconnecting` `Disconnected` duruma geçiş yapacak ve duruma geçiş yapmak `onclose` yerine geri aramaları `HubConnection` ateşleyecek ve otomatik yeniden bağlanma olmadan geri aramaları tetikleyecek. Bu, kullanıcıları bağlantının kaybolduğu konusunda uyarmak ve Kullanıcı Arabirimi öğelerini devre dışı etmek için bir fırsat sağlar.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

İstemci ilk dört denemesinde başarılı bir `HubConnection` şekilde yeniden bağlanırsa, `Connected` `onreconnected` duruma geri döner ve geri aramalarını ateşler. Bu, kullanıcılara bağlantının yeniden kurulduğunu bildirme fırsatı sağlar.

Bağlantı sunucuya tamamen yeni göründüğünden, `connectionId` `onreconnected` geri arama için yeni bir bağlantı sağlanacaktır.

> [!WARNING]
> `onreconnected` Arama nın `connectionId` parametresi, anlaşma [zatını atlamak](xref:signalr/configuration#configure-client-options)için yapılandırıldıysa `HubConnection` tanımsız olacaktır.

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()`ilk başlatma hatalarını `HubConnection` yeniden denemek için yapılandırmaz, bu nedenle başlangıç hatalarının el ile ele alınması gerekir:

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

İstemci ilk dört denemesinde başarılı bir şekilde `HubConnection` yeniden bağlanmazsa, `Disconnected` duruma geçiş ve [yakın](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramaları ateş. Bu, kullanıcılara bağlantının kalıcı olarak kaybolduğunu bildirmek ve sayfanın yenilenerek tavsiye de bulunmanızı sağlar:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel bir `withAutomaticReconnect` yeniden bağlanma denemesi sayısını yapılandırmak için, her yeniden bağlantı girişimini başlatmadan önce beklemesi için milisaniye cinsinden gecikmeyi temsil eden bir dizi sayı kabul eder.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Önceki örnek, bağlantı `HubConnection` kaybolduktan hemen sonra yeniden bağlanmayı denemeye başlamayı yapılandırır. Bu, varsayılan yapılandırma için de geçerlidir.

İlk yeniden bağlanma denemesi başarısız olursa, varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine ikinci yeniden bağlanma denemesi de hemen başlar.

İkinci yeniden bağlanma denemesi başarısız olursa, üçüncü yeniden bağlanma denemesi 10 saniye içinde başlar ve bu da varsayılan yapılandırma gibi olur.

Özel davranış daha sonra varsayılan yapılandırmada olduğu gibi başka bir 30 saniye içinde bir yeniden bağlanma girişimi denemek yerine üçüncü yeniden bağlanma girişimi hatası ndan sonra durdurarak varsayılan davranıştan yeniden uzaklaşır.

Otomatik yeniden bağlanma denemelerinin zamanlaması ve sayısı üzerinde `withAutomaticReconnect` daha fazla denetim istiyorsanız, '' adlı `IRetryPolicy` `nextRetryDelayInMilliseconds`tek bir yöntem ekibe uygulayan bir nesne kabul eder.

`nextRetryDelayInMilliseconds`türü `RetryContext`ile tek bir bağımsız değişken alır. Üç `RetryContext` `previousRetryCount`özelliği vardır: `elapsedMilliseconds` `retryReason` , ve `number`hangi `number` a `Error` , a ve bir sırasıyla. İlk yeniden bağlanma denemesinden `previousRetryCount` `elapsedMilliseconds` önce, her ikisi `retryReason` de ve sıfır olacak ve bağlantının kaybolmasına neden olan Hata olacaktır. Her başarısız yeniden deneme `previousRetryCount` denemesinden sonra, bir `elapsedMilliseconds` ile artımlı olacak, milisaniye cinsinden yeniden bağlanma için harcanan süreyi yansıtacak şekilde güncellenecektir ve son yeniden bağlanma denemesinin başarısız olması için hata `retryReason` olacaktır.

`nextRetryDelayInMilliseconds`bir sonraki yeniden bağlanma girişiminden önce beklemek için milisaniye sayısını `null` temsil `HubConnection` eden bir sayı döndürmesi veya yeniden bağlanmayı durdurması gerekir.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
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

Alternatif olarak, istemcinizi [el ile yeniden bağlayacak](#manually-reconnect)kod yazabilirsiniz.

::: moniker-end

### <a name="manually-reconnect"></a>El ile yeniden bağlanma

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3.0'dan önce, javascript SignalR istemcisi otomatik olarak yeniden bağlanmaz. İstemcinizi el ile yeniden bağlayacak kod yazmanız gerekir.

::: moniker-end

Aşağıdaki kod tipik bir el ile yeniden bağlantı yaklaşımını gösterir:

1. Bağlantıyı başlatmak için bir `start` işlev (bu durumda, işlev) oluşturulur.
1. Bağlantının `start` `onclose` olay işleyicisindeki işlevi çağırın.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Gerçek bir uygulama, vazgeçmeden önce üstel bir geri çekilme kullanır veya belirli sayıda yeniden dener.

## <a name="additional-resources"></a>Ek kaynaklar

* [JavaScript API'si başvurusu](/javascript/api/?view=signalr-js-latest)
* [JavaScript öğretici](xref:tutorials/signalr)
* [WebPack ve TypeScript öğretici](xref:tutorials/signalr-typescript-webpack)
* [Hub'lar](xref:signalr/hubs)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Çapraz Orijin İstekleri (CORS)](xref:security/cors)
* [Azure SignalR Hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)
