---
title: ASP.NET Core'da WebSockets desteği
author: rick-anderson
description: ASP.NET Core'da WebSockets ile nasıl başlayın öğrenin.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655787"
---
# <a name="websockets-support-in-aspnet-core"></a>ASP.NET Core'da WebSockets desteği

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-Nurse](https://github.com/anurse)

Bu makalede, ASP.NET Core'daki WebSockets ile nasıl başlatı yapılacağını açıklanmaktadır. [WebSocket](https://wikipedia.org/wiki/WebSocket) [(RFC 6455),](https://tools.ietf.org/html/rfc6455)TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür. Sohbet, pano ve oyun uygulamaları gibi hızlı ve gerçek zamanlı iletişimden yararlanan uygulamalarda kullanılır.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample) [Nasıl çalıştırın](#sample-app).

## <a name="signalr"></a>SignalR

[ASP.NET Core SignalR](xref:signalr/introduction) uygulamalara gerçek zamanlı web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır. Mümkün olduğunda WebSockets kullanır.

Çoğu uygulama için, signalr'i ham WebSockets üzerinden öneririz. SignalR, WebSockets'in kullanılamadığı ortamlar için aktarım geri dönüşleri sağlar. Ayrıca basit bir uzaktan yordam arama uygulaması modeli sağlar. Ve çoğu senaryoda, SignalR ham WebSockets kullanarak karşılaştırıldığında önemli bir performans dezavantajı vardır.

## <a name="prerequisites"></a>Ön koşullar

* ASP.NET Core 1.1 veya sonraki
* ASP.NET Core destekleyen herhangi bir işletim sistemi:
  
  * Windows 7 / Windows Server 2008 veya sonrası
  * Linux
  * macOS
  
* Uygulama IIS ile Windows'da çalışıyorsa:

  * Windows 8 / Windows Server 2012 veya sonrası
  * IIS 8 / IIS 8 Ekspres
  * WebSockets etkinleştirilmelidir [(Bkz. IIS/IIS Express destek](#iisiis-express-support) bölümü.).
  
* Uygulama [HTTP.sys'de çalışıyorsa:](xref:fundamentals/servers/httpsys)

  * Windows 8 / Windows Server 2012 veya sonrası

* Desteklenen tarayıcılar için https://caniuse.com/#feat=websocketsbkz.

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>NuGet paketi

[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) paketini yükleyin.

::: moniker-end

## <a name="configure-the-middleware"></a>Ara yazılımı yapılandırma


WebSockets ara yazılımını `Configure` sınıfın yöntemine `Startup` ekleyin:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Aşağıdaki ayarlar yapılandırılabilir:

* `KeepAliveInterval`- Yakınlık ların bağlantıyı açık tutmasını sağlamak için istemciye "ping" çerçeveleri ne sıklıkta gönderilir. Varsayılan değer iki dakikadır.
* `ReceiveBufferSize`- Veri almak için kullanılan arabelleğe boyutu. Gelişmiş kullanıcıların verilerin boyutuna bağlı olarak performans atoması için bunu değiştirmeleri gerekebilir. Varsayılan değer 4 KB'dir.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Aşağıdaki ayarlar yapılandırılabilir:

* `KeepAliveInterval`- Yakınlık ların bağlantıyı açık tutmasını sağlamak için istemciye "ping" çerçeveleri ne sıklıkta gönderilir. Varsayılan değer iki dakikadır.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Veri almak için kullanılan arabelleğe boyutu. Gelişmiş kullanıcıların verilerin boyutuna bağlı olarak performans atoması için bunu değiştirmeleri gerekebilir. Varsayılan değer 4 KB'dir.
* `AllowedOrigins`- WebSocket istekleri için izin verilen Başlangıç üstbilgi değerlerinin listesi. Varsayılan olarak, tüm kökenlere izin verilir. Ayrıntılar için aşağıdaki "WebSocket başlangıç kısıtlaması" başlıklı sayfaya bakın.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>WebSocket isteklerini kabul et

Daha sonra istek yaşam döngüsünün `Configure` ilerleyen bir yerinde (daha sonra yöntemde veya örneğin bir eylem yönteminde) WebSocket isteği olup olmadığını kontrol edin ve WebSocket isteğini kabul edin.

Aşağıdaki örnek yöntemde `Configure` daha sonra dan:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Bir WebSocket isteği herhangi bir URL'de gelebilir, ancak `/ws`bu örnek kod yalnızca .

Bir WebSocket kullanırken, ara yazılım ardışık hattını bağlantı süresince çalışır durumda tutmanız **gerekir.** Ara yazılım ardışık noktası sona erdikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Bir WebSocket'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık lığını çalışır durumda tuttuğunuzdan emin olun. Bunu bir <xref:System.Threading.Tasks.TaskCompletionSource%601>. Arka `TaskCompletionSource` plan hizmetinize aktarın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> ve WebSocket ile bitirdiğinizde aramasını sorun. Daha `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> sonra aşağıdaki örnekte gösterildiği gibi, istek sırasında özellik:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
Bir eylem yönteminden çok erken dönerseniz, WebSocket kapalı özel durum da gerçekleşebilir. Bir işlem yönteminde bir soketkabul ederseniz, işlem yönteminden dönmeden önce soketi kullanan kodun tamamlanmasını bekleyin.

Ciddi `Task.Wait()`iş `Task.Result`parçacığı sorunlarına neden olabileceğinden, soketin tamamlanmasını beklemek için asla , veya benzeri engelleme çağrılarını kullanmayın. Her `await`zaman kullanın.

## <a name="send-and-receive-messages"></a>İleti alma ve gönderme

Yöntem, `AcceptWebSocketAsync` TCP bağlantısını Bir WebSocket bağlantısına yükseltir ve bir [WebSocket nesnesi](/dotnet/core/api/system.net.websockets.websocket) sağlar. İleti `WebSocket` göndermek ve almak için nesneyi kullanın.

WebSocket isteğini kabul eden daha önce `WebSocket` gösterilen kod `Echo` nesneyi bir yönteme geçirir. Kod bir ileti alır ve hemen aynı iletiyi geri gönderir. İstemci bağlantıyı kapatana kadar iletiler bir döngü içinde gönderilir ve alınır:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Döngüyü başlamadan önce WebSocket bağlantısını kabul ederken, ara yazılım ardışık alanı sona erer. Soketi kapattıktan sonra boru hattı gonyor. Diğer bir deyişle, WebSocket kabul edildiğinde istek ardışık yönde ilerlemeyi durdurur. Döngü bittiğinde ve soket kapatıldığında, istek ardışık alt ardışık tarafından geri gelir.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>İşle istemci bağlantısını keser

Bağlantı kaybı nedeniyle istemci bağlantı kesildiğinde sunucu otomatik olarak bilgilendirilmez. Sunucu, yalnızca istemci gönderirse bağlantı kesme iletisi alır ve bu da internet bağlantısı kaybolursa yapılamaz. Bu durumda bazı eylemlerde bulunmanızı istiyorsanız, belirli bir zaman penceresi içinde istemciden hiçbir şey alınmadıktan sonra bir zaman aralay.'

İstemci her zaman ileti göndermiyorsa ve bağlantı boşta kaldığı için zaman lamak istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermek için bir zamanlayıcı kullanmasını bekleyin. Sunucuda, bir ileti bir öncekinden\*2 X saniye sonra gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin. Ping iletisini geciktirebilecek ağ gecikmeleri için fazladan zaman bırakmak için beklenen zaman aralığının iki katını bekleyin.

## <a name="websocket-origin-restriction"></a>WebSocket kaynak kısıtlaması

CORS tarafından sağlanan korumalar WebSockets için geçerli değildir. Tarayıcılar **yok:**

* CORS uçuş öncesi isteklerini yerine getirin.
* WebSocket isteklerini `Access-Control` yaparken üstbilgide belirtilen kısıtlamalara saygı gösterin.

Ancak, tarayıcılar WebSocket isteklerini `Origin` verirken üstbilgi gönderir. Uygulamalar, yalnızca beklenen kaynaklardan gelen WebSocket'lere izin verildiğinden emin olmak için bu üstbilgi işlemlerini doğrulayacak şekilde yapılandırılmalıdır.

Sunucunuzuhttps://server.com" " ve " " üzerindehttps://client.comistemcinizi barındırıyorsanız, WebSockets'in doğrulamak içinhttps://client.com `AllowedOrigins` listeye " ekle" ekleyin.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> Üstbilgi `Origin` istemci tarafından denetlenir ve `Referer` üstbilgi gibi sahte olabilir. Bu üstbilgi bir kimlik doğrulama mekanizması olarak **kullanmayın.**

::: moniker-end

## <a name="iisiis-express-support"></a>IIS/IIS Express desteği

Windows Server 2012 veya sonrası ve Windows 8 veya daha sonra IIS/IIS Express 8 veya sonraki websocket protokolü için destek vardır.

> [!NOTE]
> IIS Express kullanırken WebSockets her zaman etkinleştirilir.

### <a name="enabling-websockets-on-iis"></a>IIS'de WebSockets'i etkinleştirme

Windows Server 2012 veya sonraki web socket protokolü için destek etkinleştirmek için:

> [!NOTE]
> IIS Express kullanırken bu adımlar gerekli değildir

1. **Yönet** menüsünden veya **Server Manager'daki**bağlantıdan Rol ve **Özellikler Ekle** sihirbazını kullanın.
1. **Rol Tabanlı veya Özellik Tabanlı Yükleme'yi**seçin. **Sonraki'ni**seçin.
1. Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilir). **Sonraki'ni**seçin.
1. **Roller** ağacında **Web Sunucusu'nu (IIS)** genişletin, **Web Sunucusu'nu**genişletin ve **ardından Uygulama Geliştirme'yi genişletin.**
1. **WebSocket Protokolü'nü**seçin. **Sonraki'ni**seçin.
1. Ek özellikler gerekli değilse, **İleri'yi**seçin.
1. **Yükle**’yi seçin.
1. Yükleme tamamlandığında, sihirbazdan çıkmak için **Kapat'ı** seçin.

Windows 8 veya sonraki web socket protokolü için destek etkinleştirmek için:

> [!NOTE]
> IIS Express kullanırken bu adımlar gerekli değildir

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında)**açın.**
1. Aşağıdaki düğümleri açın: **İnternet Bilgi Hizmetleri** > **World Wide Web Services** > **Uygulama Geliştirme Özellikleri**.
1. **WebSocket Protokolü** özelliğini seçin. **Tamam'ı**seçin.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Düğümde socket.io kullanırken WebSocket'i devre dışı

[Node.js](https://nodejs.org/) [üzerinde socket.io](https://socket.io/) WebSocket desteği kullanıyorsanız, *web.config* veya *applicationHost.config* `webSocket` öğesi kullanarak varsayılan IIS WebSocket modülü devre dışı. Bu adım gerçekleştirilmezse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Örnek uygulama

Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir yankı uygulamasıdır. WebSocket bağlantılarını oluşturan bir web sayfası vardır ve sunucu aldığı tüm iletileri istemciye geri gönderir. Uygulamayı bir komut isteminden çalıştırın (IIS Express ile Visual Studio'dan çalışacak http://localhost:5000şekilde ayarlanmaz) ve 'ye gidin. Web sayfası sol üstteki bağlantı durumunu gösterir:

![Web sayfasının ilk durumu](websockets/_static/start.png)

Gösterilen URL'ye WebSocket isteği göndermek için **Bağlan'ı** seçin. Bir test iletisi girin ve **Gönder'i**seçin. Bittiğinde, **Soketi Kapat'ı**seçin. **İletişim Günlüğü** bölümü, her bir açık, gönder ve eylemi olduğu gibi kapatır.

![Web sayfasının ilk durumu](websockets/_static/end.png)

