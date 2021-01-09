---
title: ASP.NET Core desteği WebSockets
author: rick-anderson
description: ASP.NET Core 'de WebSockets kullanmaya başlama hakkında bilgi edinin.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
no-loc:
- appsettings.json
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058356"
---
# <a name="websockets-support-in-aspnet-core"></a>ASP.NET Core desteği WebSockets

[Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-nurte](https://github.com/anurse)

Bu makalede, ASP.NET Core ' de WebSockets ile çalışmaya başlama açıklanmaktadır. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)), TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür. Bu, sohbet, pano ve oyun uygulamaları gibi hızlı, gerçek zamanlı iletişimden faydalanabilir uygulamalarda kullanılır.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)). [Nasıl çalıştırılır?](#sample-app)

## SignalR

[ASP.NET Core SignalR ](xref:signalr/introduction) , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır. Mümkün olduğunda WebSockets kullanır.

Çoğu uygulama için SignalR Ham WebSockets üzerinde önerilir. SignalR WebSockets ' nin kullanılamadığı ortamlar için taşıma geri dönüşü sağlar. Ayrıca temel bir uzak yordam çağrısı uygulama modeli sağlar. Ve çoğu senaryoda SignalR Ham WebSockets kullanmaya kıyasla önemli bir performans olumsuz yanı yoktur.

Bazı uygulamalarda, [.net 'Teki GRPC](xref:grpc/index) , WebSockets için bir alternatif sağlar.

## <a name="prerequisites"></a>Önkoşullar

* ASP.NET Core destekleyen herhangi bir işletim sistemi:  
  * Windows 7/Windows Server 2008 veya üzeri
  * Linux
  * macOS  
* Uygulama IIS ile Windows üzerinde çalışıyorsa:
  * Windows 8/Windows Server 2012 veya üzeri
  * IIS 8/IIS 8 Express
  * WebSockets etkinleştirilmelidir. Bkz. [IIS/IIS Express desteği](#iisiis-express-support) bölümü.  
* Uygulama [HTTP.sys](xref:fundamentals/servers/httpsys)üzerinde çalışıyorsa:
  * Windows 8/Windows Server 2012 veya üzeri
* Desteklenen tarayıcılar için bkz https://caniuse.com/#feat=websockets ..

## <a name="configure-the-middleware"></a>Ara yazılımı yapılandırma

WebSockets ara yazılımını `Configure` sınıfının yöntemine ekleyin `Startup` :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Aşağıdaki ayarlar yapılandırılabilir:

* `KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı. Varsayılan değer iki dakikadır.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Aşağıdaki ayarlar yapılandırılabilir:

* `KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı. Varsayılan değer iki dakikadır.
* `AllowedOrigins` -WebSocket istekleri için izin verilen kaynak üst bilgi değerleri listesi. Varsayılan olarak, tüm kaynaklardan izin verilir. Ayrıntılar için aşağıdaki "WebSocket kaynak kısıtlaması" başlığına bakın.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>WebSocket isteklerini kabul et

Daha sonra istek yaşam döngüsünün (daha sonra `Configure` yöntemde veya bir eylem yönteminde daha sonra) bir yerde, bir WebSocket isteği olup olmadığını denetleyin ve WebSocket isteğini kabul edin.

Aşağıdaki örnek daha sonra `Configure` yönteminde verilmiştir:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Herhangi bir URL 'de bir WebSocket isteği gelebilir, ancak bu örnek kod yalnızca için istekleri kabul eder `/ws` .

WebSocket kullanırken, bağlantı süresince ara yazılım işlem hattını çalışır durumda tutmanız **gerekir** . Ara yazılım ardışık düzeni bittikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

WebSocket 'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık düzenini çalışır durumda tutmanız gerekir. Bunu kullanarak yapın <xref:System.Threading.Tasks.TaskCompletionSource%601> . `TaskCompletionSource`' İ arka plan hizmetinize geçirin ve WebSocket ile bitirdiğinizde bu hizmete çağrı yapın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> . Ardından `await` , <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> Aşağıdaki örnekte gösterildiği gibi istek sırasında özelliği:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

WebSocket kapalı özel durumu bir eylem yönteminden çok yakında döndürüldükten sonra da gerçekleşebilir. Bir eylem yönteminde yuva kabul edilirken, işlem yönteminden dönmeden önce yuva kullanan kodun tamamlanmasını bekleyin.

`Task.Wait` `Task.Result` Önemli iş parçacığı sorunlarına neden olabileceği için, yuvanın tamamlanmasını beklemek için hiçbir şekilde, veya benzer engelleme çağrılarını kullanmayın. Her zaman kullanın `await` .

## <a name="send-and-receive-messages"></a>İleti alma ve gönderme

`AcceptWebSocketAsync`Yöntemi, TCP bağlantısını bir WebSocket bağlantısıyla yükseltir ve bir [WebSocket](/dotnet/core/api/system.net.websockets.websocket) nesnesi sağlar. `WebSocket`İleti göndermek ve almak için nesnesini kullanın.

WebSocket isteğini kabul eden daha önce gösterilen kod, `WebSocket` nesneyi bir `Echo` yönteme geçirir. Kod bir ileti alır ve hemen aynı iletiyi geri gönderir. İletiler, istemci bağlantıyı kapatana kadar bir döngüde gönderilir ve alınır:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Döngüye başlamadan önce WebSocket bağlantısı kabul edildiğinde, ara yazılım ardışık düzeni sona erer. Yuva kapatıldıktan sonra işlem hattı kaldırımları. Diğer bir deyişle, WebSocket kabul edildiğinde isteğin işlem hattında ileri doğru hareket ettirilmesi durduruluyor. Döngü bittiğinde ve yuva kapalıyken, istek ardışık düzeni yedekler.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>İstemci bağlantısı kesilen işleme

İstemci bağlantı kaybı nedeniyle bağlantısı kesildiğinde sunucu otomatik olarak bilgilendirilmedi. Sunucu, yalnızca istemci gönderirse, internet bağlantısı kaybedilmişse gerçekleştirilemez bir bağlantı kesme iletisi alır. Bu durumda bazı işlemleri gerçekleştirmek istiyorsanız, belirli bir zaman penceresinde istemciden hiçbir şey alınmadığında bir zaman aşımı ayarlayın.

İstemci her zaman ileti göndermiyor ve bağlantı boşta kaldığı için zaman aşımına uğramasını istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermesi için bir Zamanlayıcı kullanmasını sağlamak için bir Zamanlayıcı kullanmasını sağlayabilirsiniz. Sunucusunda, bir ileti \* öncekinden sonra 2 X saniye içinde gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin. Beklenen zaman aralığının iki kez, ping iletisini tutabilecek Ağ gecikmeleri için ek süre kalmasını bekleyin.

## <a name="websocket-origin-restriction"></a>WebSocket kaynak kısıtlaması

CORS tarafından sunulan korumalar WebSockets için geçerlidir. Tarayıcılar şunları **desteklemez**:

* CORS ön uçuş istekleri gerçekleştirin.
* `Access-Control`WebSocket istekleri yapılırken üst bilgilerde belirtilen kısıtlamalara saygı.

Ancak, tarayıcılar `Origin` WebSocket istekleri verirken üstbilgiyi gönderir. Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.

Sunucunuzu "" üzerinde barındırıyorsanız https://server.com ve istemcinizi "" üzerinde barındırıyorsanız https://client.com , https://client.com `AllowedOrigins` doğrulamak için WebSockets listesine "" ekleyin.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> `Origin`Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir. Bu üst bilgileri kimlik doğrulama mekanizması **olarak kullanmayın.**

::: moniker-end

## <a name="iisiis-express-support"></a>IIS/IIS Express desteği

IIS/IIS Express 8 veya üzeri ile Windows Server 2012 veya üzeri ve Windows 8 veya üzeri, WebSocket protokolü için destek içerir.

> [!NOTE]
> IIS Express kullanılırken WebSockets her zaman etkindir.

### <a name="enabling-websockets-on-iis"></a>IIS üzerinde WebSockets etkinleştirme

Windows Server 2012 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:

> [!NOTE]
> IIS Express kullanılırken bu adımlar gerekli değildir

1. **Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın.
1. **Rol tabanlı veya özellik tabanlı yükleme**' yi seçin. **İleri**’yi seçin.
1. Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilidir). **İleri**’yi seçin.
1. **Roller** ağacında **Web sunucusu (IIS)** öğesini genişletin, **Web sunucusu**' nu genişletin ve ardından **uygulama geliştirme**' yi genişletin.
1. **WebSocket protokolünü** seçin. **İleri**’yi seçin.
1. Ek özellikler gerekmiyorsa, **İleri**' yi seçin.
1. **Yükle**'yi seçin.
1. Yükleme tamamlandığında sihirbazdan çıkmak için **Kapat** ' ı seçin.

Windows 8 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:

> [!NOTE]
> IIS Express kullanılırken bu adımlar gerekli değildir

1. **Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).
1. Şu düğümleri açın: **Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özellikleri**.
1. **WebSocket protokolü** özelliğini seçin. **Tamam**’ı seçin.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Node.js üzerinde socket.io kullanırken WebSocket 'i devre dışı bırak

[Node.js](https://nodejs.org/)üzerinde [Socket.io](https://socket.io/) ' de WebSocket DESTEĞINI kullanıyorsanız, varsayılan IIS WebSocket modülünü `webSocket` *web.config* veya *applicationHost.config* öğesini kullanarak devre dışı bırakın. Bu adım gerçekleştirilmemişse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Örnek uygulama

Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir Echo uygulamasıdır. WebSocket bağlantısı yapan bir Web sayfasına sahiptir ve sunucu istemciye geri aldığı tüm iletileri daha sonra sonlandırır. Örnek uygulama, IIS Express ile Visual Studio 'dan çalışacak şekilde yapılandırılmamış, bu nedenle uygulamayı ile bir komut kabuğunda çalıştırın ve ' [`dotnet run`](/dotnet/core/tools/dotnet-run) a bir tarayıcıda gidin `http://localhost:5000` . Web sayfası bağlantı durumunu gösterir:

![WebSockets bağlantısından önce Web sayfasının ilk durumu](websockets/_static/start.png)

Gösterilen URL 'ye WebSocket isteği göndermek için **Bağlan** ' ı seçin. Bir sınama iletisi girin ve **Gönder**' i seçin. İşiniz bittiğinde **yuvayı kapat**' ı seçin. **Iletişim günlüğü** bölümünde her açık, gönder ve Kapat eylemi gerçekleşir.

![WebSockets bağlantısı ve test iletileri gönderildikten ve alındıktan sonra Web sayfasının son durumu](websockets/_static/end.png)
