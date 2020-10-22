---
title: ASP.NET Core SignalR bağlantı sorunlarını giderme
author: bradygaster
description: ASP.NET Core SignalR bağlantı sorunlarını giderme.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: 2ffe8bcf4bef5dcb9fc74513c7507e5cb3a6b7cb
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379581"
---
# <a name="troubleshoot-connection-errors"></a>Bağlantı hatalarını giderme

Bu bölüm, bir ASP.NET Core hub 'ına bağlantı kurmaya çalışırken oluşabilecek hatalarla ilgili yardım sağlar SignalR .

### <a name="response-code-404"></a>Yanıt kodu 404

WebSockets ve `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Yapışkan oturumlar olmadan birden çok sunucu kullanırken, bağlantı bir sunucuda başlayabilir ve ardından başka bir sunucuya geçiş yapabilir. Diğer sunucu önceki bağlantıyı bilmez.
* İstemcinin doğru uç noktaya bağlanmakta olduğunu doğrulayın. Örneğin, sunucusu adresinde barındırılır `http://127.0.0.1:5000/hub/myHub` ve istemci, ' a bağlanmaya çalışıyor `http://127.0.0.1:5000/myHub` .
* Bağlantı KIMLIĞI kullanıyorsa ve anlaşdıktan sonra sunucuya istek göndermek için çok uzun sürerse, sunucu:

  * KIMLIĞI siler.
  * Bir 404 döndürür.

### <a name="response-code-400-or-503"></a>Yanıt kodu 400 veya 503

Aşağıdaki hata için:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Bu hata genellikle yalnızca WebSockets taşıması kullanan bir istemci tarafından oluşur ancak WebSockets Protokolü sunucuda etkinleştirilmemiş olur.

### <a name="response-code-307"></a>Yanıt kodu 307

WebSockets ve `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Bu hata, anlaşma isteği sırasında da meydana gelebilir.

Yaygın neden:
* Uygulama, URL yeniden yazma kuralına girerek https 'yi zorlamak üzere yapılandırılmıştır `UseHttpsRedirection` `Startup` veya https 'yi uygular.

Olası çözüm:
* İstemci tarafındaki URL 'YI "http" iken "https" olarak değiştirin. `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Yanıt kodu 405

Http durum kodu 405-Yönteme Izin verilmiyor

* Uygulamanın [CORS](xref:signalr/security#cross-origin-resource-sharing) özelliği etkin değil

### <a name="response-code-0"></a>Yanıt kodu 0

Http durum kodu 0-genellikle bir [CORS](xref:signalr/security#cross-origin-resource-sharing) sorunu, hiçbir durum kodu verilmez

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Beklenen kaynakları ekle `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* `.AllowCredentials()`CORS ilkenize ekleyin. `.AllowAnyOrigin()` `.WithOrigins("*")` Bu seçenekle veya kullanılamaz

### <a name="response-code-413"></a>Yanıt kodu 413

Http durum kodu 413-yük çok büyük

Bu, genellikle 4k üzerinde olan bir erişim belirtecine sahip olunmasından kaynaklanır.

* Azure SignalR hizmetini kullanıyorsanız, hizmetle birlikte gönderilen talepleri özelleştirerek belirteç boyutunu azaltın:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Geçici ağ sorunları

Geçici ağ arızaları SignalR bağlantıyı kapatabilir. Sunucu, kapalı bağlantıyı düzgün bir istemci bağlantısı kesilme olarak yorumlayabilir. Bu durumlarda istemcinin bağlantısı kesilmesinin neden olduğu hakkında daha fazla bilgi edinmek için [istemciden ve sunucudan günlükleri toplayın](xref:signalr/diagnostics).