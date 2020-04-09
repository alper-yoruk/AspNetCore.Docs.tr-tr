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
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="e280e-103">ASP.NET Core'da WebSockets desteği</span><span class="sxs-lookup"><span data-stu-id="e280e-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="e280e-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e280e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="e280e-105">Bu makalede, ASP.NET Core'daki WebSockets ile nasıl başlatı yapılacağını açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e280e-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="e280e-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) [(RFC 6455),](https://tools.ietf.org/html/rfc6455)TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="e280e-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="e280e-107">Sohbet, pano ve oyun uygulamaları gibi hızlı ve gerçek zamanlı iletişimden yararlanan uygulamalarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e280e-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="e280e-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="e280e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="e280e-109">[Nasıl çalıştırın](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="e280e-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="e280e-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="e280e-110">SignalR</span></span>

<span data-ttu-id="e280e-111">[ASP.NET Core SignalR](xref:signalr/introduction) uygulamalara gerçek zamanlı web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="e280e-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="e280e-112">Mümkün olduğunda WebSockets kullanır.</span><span class="sxs-lookup"><span data-stu-id="e280e-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="e280e-113">Çoğu uygulama için, signalr'i ham WebSockets üzerinden öneririz.</span><span class="sxs-lookup"><span data-stu-id="e280e-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="e280e-114">SignalR, WebSockets'in kullanılamadığı ortamlar için aktarım geri dönüşleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="e280e-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="e280e-115">Ayrıca basit bir uzaktan yordam arama uygulaması modeli sağlar.</span><span class="sxs-lookup"><span data-stu-id="e280e-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="e280e-116">Ve çoğu senaryoda, SignalR ham WebSockets kullanarak karşılaştırıldığında önemli bir performans dezavantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="e280e-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e280e-117">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="e280e-117">Prerequisites</span></span>

* <span data-ttu-id="e280e-118">ASP.NET Core 1.1 veya sonraki</span><span class="sxs-lookup"><span data-stu-id="e280e-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="e280e-119">ASP.NET Core destekleyen herhangi bir işletim sistemi:</span><span class="sxs-lookup"><span data-stu-id="e280e-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="e280e-120">Windows 7 / Windows Server 2008 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="e280e-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="e280e-121">Linux</span><span class="sxs-lookup"><span data-stu-id="e280e-121">Linux</span></span>
  * <span data-ttu-id="e280e-122">macOS</span><span class="sxs-lookup"><span data-stu-id="e280e-122">macOS</span></span>
  
* <span data-ttu-id="e280e-123">Uygulama IIS ile Windows'da çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="e280e-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="e280e-124">Windows 8 / Windows Server 2012 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="e280e-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="e280e-125">IIS 8 / IIS 8 Ekspres</span><span class="sxs-lookup"><span data-stu-id="e280e-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="e280e-126">WebSockets etkinleştirilmelidir [(Bkz. IIS/IIS Express destek](#iisiis-express-support) bölümü.).</span><span class="sxs-lookup"><span data-stu-id="e280e-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="e280e-127">Uygulama [HTTP.sys'de çalışıyorsa:](xref:fundamentals/servers/httpsys)</span><span class="sxs-lookup"><span data-stu-id="e280e-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="e280e-128">Windows 8 / Windows Server 2012 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="e280e-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="e280e-129">Desteklenen tarayıcılar için https://caniuse.com/#feat=websocketsbkz.</span><span class="sxs-lookup"><span data-stu-id="e280e-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="e280e-130">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="e280e-130">NuGet package</span></span>

<span data-ttu-id="e280e-131">[Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="e280e-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="e280e-132">Ara yazılımı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e280e-132">Configure the middleware</span></span>


<span data-ttu-id="e280e-133">WebSockets ara yazılımını `Configure` sınıfın yöntemine `Startup` ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e280e-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e280e-134">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="e280e-134">The following settings can be configured:</span></span>

* <span data-ttu-id="e280e-135">`KeepAliveInterval`- Yakınlık ların bağlantıyı açık tutmasını sağlamak için istemciye "ping" çerçeveleri ne sıklıkta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="e280e-136">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="e280e-136">The default is two minutes.</span></span>
* <span data-ttu-id="e280e-137">`ReceiveBufferSize`- Veri almak için kullanılan arabelleğe boyutu.</span><span class="sxs-lookup"><span data-stu-id="e280e-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="e280e-138">Gelişmiş kullanıcıların verilerin boyutuna bağlı olarak performans atoması için bunu değiştirmeleri gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="e280e-139">Varsayılan değer 4 KB'dir.</span><span class="sxs-lookup"><span data-stu-id="e280e-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e280e-140">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="e280e-140">The following settings can be configured:</span></span>

* <span data-ttu-id="e280e-141">`KeepAliveInterval`- Yakınlık ların bağlantıyı açık tutmasını sağlamak için istemciye "ping" çerçeveleri ne sıklıkta gönderilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="e280e-142">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="e280e-142">The default is two minutes.</span></span>
* <span data-ttu-id="e280e-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Veri almak için kullanılan arabelleğe boyutu.</span><span class="sxs-lookup"><span data-stu-id="e280e-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="e280e-144">Gelişmiş kullanıcıların verilerin boyutuna bağlı olarak performans atoması için bunu değiştirmeleri gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="e280e-145">Varsayılan değer 4 KB'dir.</span><span class="sxs-lookup"><span data-stu-id="e280e-145">The default is 4 KB.</span></span>
* <span data-ttu-id="e280e-146">`AllowedOrigins`- WebSocket istekleri için izin verilen Başlangıç üstbilgi değerlerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="e280e-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="e280e-147">Varsayılan olarak, tüm kökenlere izin verilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-147">By default, all origins are allowed.</span></span> <span data-ttu-id="e280e-148">Ayrıntılar için aşağıdaki "WebSocket başlangıç kısıtlaması" başlıklı sayfaya bakın.</span><span class="sxs-lookup"><span data-stu-id="e280e-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="e280e-149">WebSocket isteklerini kabul et</span><span class="sxs-lookup"><span data-stu-id="e280e-149">Accept WebSocket requests</span></span>

<span data-ttu-id="e280e-150">Daha sonra istek yaşam döngüsünün `Configure` ilerleyen bir yerinde (daha sonra yöntemde veya örneğin bir eylem yönteminde) WebSocket isteği olup olmadığını kontrol edin ve WebSocket isteğini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="e280e-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="e280e-151">Aşağıdaki örnek yöntemde `Configure` daha sonra dan:</span><span class="sxs-lookup"><span data-stu-id="e280e-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="e280e-152">Bir WebSocket isteği herhangi bir URL'de gelebilir, ancak `/ws`bu örnek kod yalnızca .</span><span class="sxs-lookup"><span data-stu-id="e280e-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="e280e-153">Bir WebSocket kullanırken, ara yazılım ardışık hattını bağlantı süresince çalışır durumda tutmanız **gerekir.**</span><span class="sxs-lookup"><span data-stu-id="e280e-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="e280e-154">Ara yazılım ardışık noktası sona erdikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e280e-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="e280e-155">Bir WebSocket'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık lığını çalışır durumda tuttuğunuzdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="e280e-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="e280e-156">Bunu bir <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="e280e-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="e280e-157">Arka `TaskCompletionSource` plan hizmetinize aktarın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> ve WebSocket ile bitirdiğinizde aramasını sorun.</span><span class="sxs-lookup"><span data-stu-id="e280e-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="e280e-158">Daha `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> sonra aşağıdaki örnekte gösterildiği gibi, istek sırasında özellik:</span><span class="sxs-lookup"><span data-stu-id="e280e-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="e280e-159">Bir eylem yönteminden çok erken dönerseniz, WebSocket kapalı özel durum da gerçekleşebilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="e280e-160">Bir işlem yönteminde bir soketkabul ederseniz, işlem yönteminden dönmeden önce soketi kullanan kodun tamamlanmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="e280e-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="e280e-161">Ciddi `Task.Wait()`iş `Task.Result`parçacığı sorunlarına neden olabileceğinden, soketin tamamlanmasını beklemek için asla , veya benzeri engelleme çağrılarını kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="e280e-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="e280e-162">Her `await`zaman kullanın.</span><span class="sxs-lookup"><span data-stu-id="e280e-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="e280e-163">İleti alma ve gönderme</span><span class="sxs-lookup"><span data-stu-id="e280e-163">Send and receive messages</span></span>

<span data-ttu-id="e280e-164">Yöntem, `AcceptWebSocketAsync` TCP bağlantısını Bir WebSocket bağlantısına yükseltir ve bir [WebSocket nesnesi](/dotnet/core/api/system.net.websockets.websocket) sağlar.</span><span class="sxs-lookup"><span data-stu-id="e280e-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="e280e-165">İleti `WebSocket` göndermek ve almak için nesneyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="e280e-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="e280e-166">WebSocket isteğini kabul eden daha önce `WebSocket` gösterilen kod `Echo` nesneyi bir yönteme geçirir.</span><span class="sxs-lookup"><span data-stu-id="e280e-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="e280e-167">Kod bir ileti alır ve hemen aynı iletiyi geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="e280e-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="e280e-168">İstemci bağlantıyı kapatana kadar iletiler bir döngü içinde gönderilir ve alınır:</span><span class="sxs-lookup"><span data-stu-id="e280e-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="e280e-169">Döngüyü başlamadan önce WebSocket bağlantısını kabul ederken, ara yazılım ardışık alanı sona erer.</span><span class="sxs-lookup"><span data-stu-id="e280e-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="e280e-170">Soketi kapattıktan sonra boru hattı gonyor.</span><span class="sxs-lookup"><span data-stu-id="e280e-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="e280e-171">Diğer bir deyişle, WebSocket kabul edildiğinde istek ardışık yönde ilerlemeyi durdurur.</span><span class="sxs-lookup"><span data-stu-id="e280e-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="e280e-172">Döngü bittiğinde ve soket kapatıldığında, istek ardışık alt ardışık tarafından geri gelir.</span><span class="sxs-lookup"><span data-stu-id="e280e-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="e280e-173">İşle istemci bağlantısını keser</span><span class="sxs-lookup"><span data-stu-id="e280e-173">Handle client disconnects</span></span>

<span data-ttu-id="e280e-174">Bağlantı kaybı nedeniyle istemci bağlantı kesildiğinde sunucu otomatik olarak bilgilendirilmez.</span><span class="sxs-lookup"><span data-stu-id="e280e-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="e280e-175">Sunucu, yalnızca istemci gönderirse bağlantı kesme iletisi alır ve bu da internet bağlantısı kaybolursa yapılamaz.</span><span class="sxs-lookup"><span data-stu-id="e280e-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="e280e-176">Bu durumda bazı eylemlerde bulunmanızı istiyorsanız, belirli bir zaman penceresi içinde istemciden hiçbir şey alınmadıktan sonra bir zaman aralay.'</span><span class="sxs-lookup"><span data-stu-id="e280e-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="e280e-177">İstemci her zaman ileti göndermiyorsa ve bağlantı boşta kaldığı için zaman lamak istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermek için bir zamanlayıcı kullanmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="e280e-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="e280e-178">Sunucuda, bir ileti bir öncekinden\*2 X saniye sonra gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin.</span><span class="sxs-lookup"><span data-stu-id="e280e-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="e280e-179">Ping iletisini geciktirebilecek ağ gecikmeleri için fazladan zaman bırakmak için beklenen zaman aralığının iki katını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="e280e-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="e280e-180">WebSocket kaynak kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="e280e-180">WebSocket origin restriction</span></span>

<span data-ttu-id="e280e-181">CORS tarafından sağlanan korumalar WebSockets için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="e280e-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="e280e-182">Tarayıcılar **yok:**</span><span class="sxs-lookup"><span data-stu-id="e280e-182">Browsers do **not**:</span></span>

* <span data-ttu-id="e280e-183">CORS uçuş öncesi isteklerini yerine getirin.</span><span class="sxs-lookup"><span data-stu-id="e280e-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="e280e-184">WebSocket isteklerini `Access-Control` yaparken üstbilgide belirtilen kısıtlamalara saygı gösterin.</span><span class="sxs-lookup"><span data-stu-id="e280e-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="e280e-185">Ancak, tarayıcılar WebSocket isteklerini `Origin` verirken üstbilgi gönderir.</span><span class="sxs-lookup"><span data-stu-id="e280e-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="e280e-186">Uygulamalar, yalnızca beklenen kaynaklardan gelen WebSocket'lere izin verildiğinden emin olmak için bu üstbilgi işlemlerini doğrulayacak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e280e-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="e280e-187">Sunucunuzuhttps://server.com" " ve " " üzerindehttps://client.comistemcinizi barındırıyorsanız, WebSockets'in doğrulamak içinhttps://client.com `AllowedOrigins` listeye " ekle" ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e280e-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="e280e-188">Üstbilgi `Origin` istemci tarafından denetlenir ve `Referer` üstbilgi gibi sahte olabilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="e280e-189">Bu üstbilgi bir kimlik doğrulama mekanizması olarak **kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="e280e-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="e280e-190">IIS/IIS Express desteği</span><span class="sxs-lookup"><span data-stu-id="e280e-190">IIS/IIS Express support</span></span>

<span data-ttu-id="e280e-191">Windows Server 2012 veya sonrası ve Windows 8 veya daha sonra IIS/IIS Express 8 veya sonraki websocket protokolü için destek vardır.</span><span class="sxs-lookup"><span data-stu-id="e280e-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="e280e-192">IIS Express kullanırken WebSockets her zaman etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e280e-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="e280e-193">IIS'de WebSockets'i etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="e280e-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="e280e-194">Windows Server 2012 veya sonraki web socket protokolü için destek etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="e280e-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="e280e-195">IIS Express kullanırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="e280e-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="e280e-196">**Yönet** menüsünden veya **Server Manager'daki**bağlantıdan Rol ve **Özellikler Ekle** sihirbazını kullanın.</span><span class="sxs-lookup"><span data-stu-id="e280e-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="e280e-197">**Rol Tabanlı veya Özellik Tabanlı Yükleme'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="e280e-198">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-198">Select **Next**.</span></span>
1. <span data-ttu-id="e280e-199">Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilir).</span><span class="sxs-lookup"><span data-stu-id="e280e-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="e280e-200">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-200">Select **Next**.</span></span>
1. <span data-ttu-id="e280e-201">**Roller** ağacında **Web Sunucusu'nu (IIS)** genişletin, **Web Sunucusu'nu**genişletin ve **ardından Uygulama Geliştirme'yi genişletin.**</span><span class="sxs-lookup"><span data-stu-id="e280e-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="e280e-202">**WebSocket Protokolü'nü**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="e280e-203">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-203">Select **Next**.</span></span>
1. <span data-ttu-id="e280e-204">Ek özellikler gerekli değilse, **İleri'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="e280e-205">**Yükle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-205">Select **Install**.</span></span>
1. <span data-ttu-id="e280e-206">Yükleme tamamlandığında, sihirbazdan çıkmak için **Kapat'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="e280e-207">Windows 8 veya sonraki web socket protokolü için destek etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="e280e-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="e280e-208">IIS Express kullanırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="e280e-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="e280e-209">Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında)**açın.**</span><span class="sxs-lookup"><span data-stu-id="e280e-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="e280e-210">Aşağıdaki düğümleri açın: **İnternet Bilgi Hizmetleri** > **World Wide Web Services** > **Uygulama Geliştirme Özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="e280e-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="e280e-211">**WebSocket Protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="e280e-212">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="e280e-213">Düğümde socket.io kullanırken WebSocket'i devre dışı</span><span class="sxs-lookup"><span data-stu-id="e280e-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="e280e-214">[Node.js](https://nodejs.org/) [üzerinde socket.io](https://socket.io/) WebSocket desteği kullanıyorsanız, *web.config* veya *applicationHost.config* `webSocket` öğesi kullanarak varsayılan IIS WebSocket modülü devre dışı. Bu adım gerçekleştirilmezse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="e280e-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="e280e-215">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="e280e-215">Sample app</span></span>

<span data-ttu-id="e280e-216">Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir yankı uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="e280e-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="e280e-217">WebSocket bağlantılarını oluşturan bir web sayfası vardır ve sunucu aldığı tüm iletileri istemciye geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="e280e-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="e280e-218">Uygulamayı bir komut isteminden çalıştırın (IIS Express ile Visual Studio'dan çalışacak http://localhost:5000şekilde ayarlanmaz) ve 'ye gidin.</span><span class="sxs-lookup"><span data-stu-id="e280e-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="e280e-219">Web sayfası sol üstteki bağlantı durumunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="e280e-219">The web page shows the connection status in the upper left:</span></span>

![Web sayfasının ilk durumu](websockets/_static/start.png)

<span data-ttu-id="e280e-221">Gösterilen URL'ye WebSocket isteği göndermek için **Bağlan'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="e280e-222">Bir test iletisi girin ve **Gönder'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="e280e-223">Bittiğinde, **Soketi Kapat'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="e280e-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="e280e-224">**İletişim Günlüğü** bölümü, her bir açık, gönder ve eylemi olduğu gibi kapatır.</span><span class="sxs-lookup"><span data-stu-id="e280e-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Web sayfasının ilk durumu](websockets/_static/end.png)

