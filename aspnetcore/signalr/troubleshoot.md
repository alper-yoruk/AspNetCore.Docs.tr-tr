---
title: 'ASP.NET Core SignalR bağlantı sorunlarını giderme'
author: bradygaster
description: 'ASP.NET Core SignalR bağlantı sorunlarını giderme.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059617"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="2693f-103">Bağlantı hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="2693f-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="2693f-104">Bu bölüm, bir ASP.NET Core hub 'ına bağlantı kurmaya çalışırken oluşabilecek hatalarla ilgili yardım sağlar SignalR .</span><span class="sxs-lookup"><span data-stu-id="2693f-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="2693f-105">Yanıt kodu 404</span><span class="sxs-lookup"><span data-stu-id="2693f-105">Response code 404</span></span>

<span data-ttu-id="2693f-106">WebSockets ve `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="2693f-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="2693f-107">Yapışkan oturumlar olmadan birden çok sunucu kullanırken, bağlantı bir sunucuda başlayabilir ve ardından başka bir sunucuya geçiş yapabilir.</span><span class="sxs-lookup"><span data-stu-id="2693f-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="2693f-108">Diğer sunucu önceki bağlantıyı bilmez.</span><span class="sxs-lookup"><span data-stu-id="2693f-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="2693f-109">İstemcinin doğru uç noktaya bağlanmakta olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="2693f-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="2693f-110">Örneğin, sunucusu adresinde barındırılır `http://127.0.0.1:5000/hub/myHub` ve istemci, ' a bağlanmaya çalışıyor `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="2693f-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="2693f-111">Bağlantı KIMLIĞI kullanıyorsa ve anlaşdıktan sonra sunucuya istek göndermek için çok uzun sürerse, sunucu:</span><span class="sxs-lookup"><span data-stu-id="2693f-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="2693f-112">KIMLIĞI siler.</span><span class="sxs-lookup"><span data-stu-id="2693f-112">Deletes the ID.</span></span>
  * <span data-ttu-id="2693f-113">Bir 404 döndürür.</span><span class="sxs-lookup"><span data-stu-id="2693f-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="2693f-114">Yanıt kodu 400 veya 503</span><span class="sxs-lookup"><span data-stu-id="2693f-114">Response code 400 or 503</span></span>

<span data-ttu-id="2693f-115">Aşağıdaki hata için:</span><span class="sxs-lookup"><span data-stu-id="2693f-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="2693f-116">Bu hata genellikle yalnızca WebSockets taşıması kullanan bir istemci tarafından oluşur ancak WebSockets Protokolü sunucuda etkinleştirilmemiş olur.</span><span class="sxs-lookup"><span data-stu-id="2693f-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="2693f-117">Yanıt kodu 307</span><span class="sxs-lookup"><span data-stu-id="2693f-117">Response code 307</span></span>

<span data-ttu-id="2693f-118">WebSockets ve `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="2693f-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="2693f-119">Bu hata, anlaşma isteği sırasında da meydana gelebilir.</span><span class="sxs-lookup"><span data-stu-id="2693f-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="2693f-120">Yaygın neden:</span><span class="sxs-lookup"><span data-stu-id="2693f-120">Common cause:</span></span>
* <span data-ttu-id="2693f-121">Uygulama, URL yeniden yazma kuralına girerek https 'yi zorlamak üzere yapılandırılmıştır `UseHttpsRedirection` `Startup` veya https 'yi uygular.</span><span class="sxs-lookup"><span data-stu-id="2693f-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="2693f-122">Olası çözüm:</span><span class="sxs-lookup"><span data-stu-id="2693f-122">Possible solution:</span></span>
* <span data-ttu-id="2693f-123">İstemci tarafındaki URL 'YI "http" iken "https" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="2693f-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="2693f-124">Yanıt kodu 405</span><span class="sxs-lookup"><span data-stu-id="2693f-124">Response code 405</span></span>

<span data-ttu-id="2693f-125">Http durum kodu 405-Yönteme Izin verilmiyor</span><span class="sxs-lookup"><span data-stu-id="2693f-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="2693f-126">Uygulamanın [CORS](xref:signalr/security#cross-origin-resource-sharing) özelliği etkin değil</span><span class="sxs-lookup"><span data-stu-id="2693f-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="2693f-127">Yanıt kodu 0</span><span class="sxs-lookup"><span data-stu-id="2693f-127">Response code 0</span></span>

<span data-ttu-id="2693f-128">Http durum kodu 0-genellikle bir [CORS](xref:signalr/security#cross-origin-resource-sharing) sorunu, hiçbir durum kodu verilmez</span><span class="sxs-lookup"><span data-stu-id="2693f-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="2693f-129">Beklenen kaynakları ekle `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="2693f-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="2693f-130">`.AllowCredentials()`CORS ilkenize ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2693f-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="2693f-131">`.AllowAnyOrigin()` `.WithOrigins("*")` Bu seçenekle veya kullanılamaz</span><span class="sxs-lookup"><span data-stu-id="2693f-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="2693f-132">Yanıt kodu 413</span><span class="sxs-lookup"><span data-stu-id="2693f-132">Response code 413</span></span>

<span data-ttu-id="2693f-133">Http durum kodu 413-yük çok büyük</span><span class="sxs-lookup"><span data-stu-id="2693f-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="2693f-134">Bu, genellikle 4k üzerinde olan bir erişim belirtecine sahip olunmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="2693f-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="2693f-135">Azure SignalR hizmetini kullanıyorsanız, hizmetle birlikte gönderilen talepleri özelleştirerek belirteç boyutunu azaltın:</span><span class="sxs-lookup"><span data-stu-id="2693f-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="2693f-136">Geçici ağ sorunları</span><span class="sxs-lookup"><span data-stu-id="2693f-136">Transient network failures</span></span>

<span data-ttu-id="2693f-137">Geçici ağ arızaları SignalR bağlantıyı kapatabilir.</span><span class="sxs-lookup"><span data-stu-id="2693f-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="2693f-138">Sunucu, kapalı bağlantıyı düzgün bir istemci bağlantısı kesilme olarak yorumlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2693f-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="2693f-139">Bu durumlarda istemcinin bağlantısı kesilmesinin neden olduğu hakkında daha fazla bilgi edinmek için [istemciden ve sunucudan günlükleri toplayın](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2693f-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>