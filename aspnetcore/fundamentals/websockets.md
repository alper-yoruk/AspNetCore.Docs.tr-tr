---
title: ASP.NET Core desteği WebSockets
author: rick-anderson
description: ASP.NET Core 'de WebSockets kullanmaya başlama hakkında bilgi edinin.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
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
uid: fundamentals/websockets
ms.openlocfilehash: 685e694a3d974a8a51255bdbb83d33459137a3d9
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629021"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="5157a-103">ASP.NET Core desteği WebSockets</span><span class="sxs-lookup"><span data-stu-id="5157a-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="5157a-104">[Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5157a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="5157a-105">Bu makalede, ASP.NET Core ' de WebSockets ile çalışmaya başlama açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5157a-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="5157a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)), TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="5157a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="5157a-107">Bu, sohbet, pano ve oyun uygulamaları gibi hızlı, gerçek zamanlı iletişimden faydalanabilir uygulamalarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5157a-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="5157a-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5157a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="5157a-109">[Nasıl çalıştırılır?](#sample-app)</span><span class="sxs-lookup"><span data-stu-id="5157a-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="5157a-110">[ASP.NET Core SignalR ](xref:signalr/introduction) , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="5157a-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="5157a-111">Mümkün olduğunda WebSockets kullanır.</span><span class="sxs-lookup"><span data-stu-id="5157a-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="5157a-112">Çoğu uygulama için SignalR Ham WebSockets üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="5157a-113">SignalR WebSockets ' nin kullanılamadığı ortamlar için taşıma geri dönüşü sağlar.</span><span class="sxs-lookup"><span data-stu-id="5157a-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="5157a-114">Ayrıca, basit bir uzak yordam çağrısı uygulama modeli sağlar.</span><span class="sxs-lookup"><span data-stu-id="5157a-114">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="5157a-115">Ve çoğu senaryoda SignalR Ham WebSockets kullanmaya kıyasla önemli bir performans olumsuz yanı yoktur.</span><span class="sxs-lookup"><span data-stu-id="5157a-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5157a-116">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="5157a-116">Prerequisites</span></span>

* <span data-ttu-id="5157a-117">ASP.NET Core 1,1 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5157a-117">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="5157a-118">ASP.NET Core destekleyen herhangi bir işletim sistemi:</span><span class="sxs-lookup"><span data-stu-id="5157a-118">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="5157a-119">Windows 7/Windows Server 2008 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5157a-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="5157a-120">Linux</span><span class="sxs-lookup"><span data-stu-id="5157a-120">Linux</span></span>
  * <span data-ttu-id="5157a-121">macOS</span><span class="sxs-lookup"><span data-stu-id="5157a-121">macOS</span></span>
  
* <span data-ttu-id="5157a-122">Uygulama IIS ile Windows üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5157a-122">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="5157a-123">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5157a-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="5157a-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="5157a-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="5157a-125">WebSockets etkinleştirilmelidir ( [IIS/IIS Express destek](#iisiis-express-support) bölümüne bakın.).</span><span class="sxs-lookup"><span data-stu-id="5157a-125">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="5157a-126">Uygulama [HTTP.sys](xref:fundamentals/servers/httpsys)üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5157a-126">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="5157a-127">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5157a-127">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="5157a-128">Desteklenen tarayıcılar için bkz https://caniuse.com/#feat=websockets ..</span><span class="sxs-lookup"><span data-stu-id="5157a-128">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="5157a-129">NuGet paketi</span><span class="sxs-lookup"><span data-stu-id="5157a-129">NuGet package</span></span>

<span data-ttu-id="5157a-130">[Microsoft. AspNetCore. WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="5157a-130">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="5157a-131">Ara yazılımı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5157a-131">Configure the middleware</span></span>


<span data-ttu-id="5157a-132">WebSockets ara yazılımını `Configure` sınıfının yöntemine ekleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="5157a-132">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5157a-133">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="5157a-133">The following settings can be configured:</span></span>

* <span data-ttu-id="5157a-134">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="5157a-134">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="5157a-135">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5157a-135">The default is two minutes.</span></span>
* <span data-ttu-id="5157a-136">`ReceiveBufferSize` -Verileri almak için kullanılan arabelleğin boyutu.</span><span class="sxs-lookup"><span data-stu-id="5157a-136">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="5157a-137">Gelişmiş kullanıcıların, verilerin boyutuna bağlı olarak performans ayarlaması için bunu değiştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-137">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="5157a-138">Varsayılan değer 4 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="5157a-138">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5157a-139">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="5157a-139">The following settings can be configured:</span></span>

* <span data-ttu-id="5157a-140">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="5157a-140">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="5157a-141">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5157a-141">The default is two minutes.</span></span>
* <span data-ttu-id="5157a-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> -Verileri almak için kullanılan arabelleğin boyutu.</span><span class="sxs-lookup"><span data-stu-id="5157a-142"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="5157a-143">Gelişmiş kullanıcıların, verilerin boyutuna bağlı olarak performans ayarlaması için bunu değiştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-143">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="5157a-144">Varsayılan değer 4 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="5157a-144">The default is 4 KB.</span></span>
* <span data-ttu-id="5157a-145">`AllowedOrigins` -WebSocket istekleri için izin verilen kaynak üst bilgi değerleri listesi.</span><span class="sxs-lookup"><span data-stu-id="5157a-145">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="5157a-146">Varsayılan olarak, tüm kaynaklardan izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-146">By default, all origins are allowed.</span></span> <span data-ttu-id="5157a-147">Ayrıntılar için aşağıdaki "WebSocket kaynak kısıtlaması" başlığına bakın.</span><span class="sxs-lookup"><span data-stu-id="5157a-147">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="5157a-148">WebSocket isteklerini kabul et</span><span class="sxs-lookup"><span data-stu-id="5157a-148">Accept WebSocket requests</span></span>

<span data-ttu-id="5157a-149">Daha sonra istek yaşam döngüsünün (daha sonra `Configure` yöntemde veya bir eylem yönteminde daha sonra) bir yerde, bir WebSocket isteği olup olmadığını denetleyin ve WebSocket isteğini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="5157a-149">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="5157a-150">Aşağıdaki örnek daha sonra `Configure` yönteminde verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="5157a-150">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="5157a-151">Herhangi bir URL 'de bir WebSocket isteği gelebilir, ancak bu örnek kod yalnızca için istekleri kabul eder `/ws` .</span><span class="sxs-lookup"><span data-stu-id="5157a-151">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="5157a-152">WebSocket kullanırken, bağlantı süresince ara yazılım işlem hattını çalışır durumda tutmanız **gerekir** .</span><span class="sxs-lookup"><span data-stu-id="5157a-152">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="5157a-153">Ara yazılım ardışık düzeni bittikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5157a-153">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="5157a-154">WebSocket 'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık düzenini çalışır durumda tutmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5157a-154">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="5157a-155">Bunu kullanarak yapın <xref:System.Threading.Tasks.TaskCompletionSource%601> .</span><span class="sxs-lookup"><span data-stu-id="5157a-155">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="5157a-156">`TaskCompletionSource`' İ arka plan hizmetinize geçirin ve WebSocket ile bitirdiğinizde bu hizmete çağrı yapın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> .</span><span class="sxs-lookup"><span data-stu-id="5157a-156">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="5157a-157">Ardından `await` , <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> Aşağıdaki örnekte gösterildiği gibi istek sırasında özelliği:</span><span class="sxs-lookup"><span data-stu-id="5157a-157">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="5157a-158">Bir eylem yönteminden çok yakında döndürüyseniz, WebSocket kapalı özel durumu da oluşabilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-158">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="5157a-159">Bir eylem yönteminde bir yuvayı kabul ediyorsanız, işlem yönteminden dönmeden önce yuva kullanan kodun tamamlanmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="5157a-159">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="5157a-160">`Task.Wait()` `Task.Result` Önemli iş parçacığı sorunlarına neden olabileceği için, yuvanın tamamlanmasını beklemek için hiçbir şekilde, veya benzer engelleme çağrılarını kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5157a-160">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="5157a-161">Her zaman kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="5157a-161">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="5157a-162">İleti alma ve gönderme</span><span class="sxs-lookup"><span data-stu-id="5157a-162">Send and receive messages</span></span>

<span data-ttu-id="5157a-163">`AcceptWebSocketAsync`Yöntemi, TCP bağlantısını bir WebSocket bağlantısıyla yükseltir ve bir [WebSocket](/dotnet/core/api/system.net.websockets.websocket) nesnesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5157a-163">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="5157a-164">`WebSocket`İleti göndermek ve almak için nesnesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5157a-164">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="5157a-165">WebSocket isteğini kabul eden daha önce gösterilen kod, `WebSocket` nesneyi bir `Echo` yönteme geçirir.</span><span class="sxs-lookup"><span data-stu-id="5157a-165">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="5157a-166">Kod bir ileti alır ve hemen aynı iletiyi geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="5157a-166">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="5157a-167">İletiler, istemci bağlantıyı kapatana kadar bir döngüde gönderilir ve alınır:</span><span class="sxs-lookup"><span data-stu-id="5157a-167">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="5157a-168">Döngüye başlamadan önce WebSocket bağlantısı kabul edildiğinde, ara yazılım ardışık düzeni sona erer.</span><span class="sxs-lookup"><span data-stu-id="5157a-168">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="5157a-169">Yuva kapatıldıktan sonra işlem hattı kaldırımları.</span><span class="sxs-lookup"><span data-stu-id="5157a-169">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="5157a-170">Diğer bir deyişle, WebSocket kabul edildiğinde isteğin işlem hattında ileri doğru hareket ettirilmesi durduruluyor.</span><span class="sxs-lookup"><span data-stu-id="5157a-170">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="5157a-171">Döngü bittiğinde ve yuva kapalıyken, istek ardışık düzeni yedekler.</span><span class="sxs-lookup"><span data-stu-id="5157a-171">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="5157a-172">İstemci bağlantısı kesilen işleme</span><span class="sxs-lookup"><span data-stu-id="5157a-172">Handle client disconnects</span></span>

<span data-ttu-id="5157a-173">İstemci bağlantı kaybı nedeniyle bağlantısı kesildiğinde sunucu otomatik olarak bilgilendirilmedi.</span><span class="sxs-lookup"><span data-stu-id="5157a-173">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="5157a-174">Sunucu, yalnızca istemci gönderirse, internet bağlantısı kaybedilmişse gerçekleştirilemez bir bağlantı kesme iletisi alır.</span><span class="sxs-lookup"><span data-stu-id="5157a-174">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="5157a-175">Bu durumda bazı işlemleri gerçekleştirmek istiyorsanız, belirli bir zaman penceresinde istemciden hiçbir şey alınmadığında bir zaman aşımı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5157a-175">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="5157a-176">İstemci her zaman ileti göndermiyor ve bağlantı boşta kaldığı için zaman aşımına uğramasını istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermesi için bir Zamanlayıcı kullanmasını sağlamak için bir Zamanlayıcı kullanmasını sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5157a-176">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="5157a-177">Sunucusunda, bir ileti \* öncekinden sonra 2 X saniye içinde gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin.</span><span class="sxs-lookup"><span data-stu-id="5157a-177">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="5157a-178">Beklenen zaman aralığının iki kez, ping iletisini tutabilecek Ağ gecikmeleri için ek süre kalmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="5157a-178">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="5157a-179">WebSocket kaynak kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="5157a-179">WebSocket origin restriction</span></span>

<span data-ttu-id="5157a-180">CORS tarafından sunulan korumalar WebSockets için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5157a-180">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="5157a-181">Tarayıcılar şunları **desteklemez**:</span><span class="sxs-lookup"><span data-stu-id="5157a-181">Browsers do **not**:</span></span>

* <span data-ttu-id="5157a-182">CORS ön uçuş istekleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="5157a-182">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="5157a-183">`Access-Control`WebSocket istekleri yapılırken üst bilgilerde belirtilen kısıtlamalara saygı.</span><span class="sxs-lookup"><span data-stu-id="5157a-183">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="5157a-184">Ancak, tarayıcılar `Origin` WebSocket istekleri verirken üstbilgiyi gönderir.</span><span class="sxs-lookup"><span data-stu-id="5157a-184">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="5157a-185">Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5157a-185">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="5157a-186">Sunucunuzu "" üzerinde barındırıyorsanız https://server.com ve istemcinizi "" üzerinde barındırıyorsanız https://client.com , https://client.com `AllowedOrigins` doğrulamak için WebSockets listesine "" ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5157a-186">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="5157a-187">`Origin`Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="5157a-187">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="5157a-188">Bu üst bilgileri kimlik doğrulama mekanizması **olarak kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="5157a-188">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="5157a-189">IIS/IIS Express desteği</span><span class="sxs-lookup"><span data-stu-id="5157a-189">IIS/IIS Express support</span></span>

<span data-ttu-id="5157a-190">IIS/IIS Express 8 veya üzeri ile Windows Server 2012 veya üzeri ve Windows 8 veya üzeri, WebSocket protokolü için destek içerir.</span><span class="sxs-lookup"><span data-stu-id="5157a-190">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="5157a-191">IIS Express kullanılırken WebSockets her zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="5157a-191">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="5157a-192">IIS üzerinde WebSockets etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="5157a-192">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="5157a-193">Windows Server 2012 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="5157a-193">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="5157a-194">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="5157a-194">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="5157a-195">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi**bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5157a-195">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="5157a-196">**Rol tabanlı veya özellik tabanlı yükleme**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-196">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="5157a-197">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-197">Select **Next**.</span></span>
1. <span data-ttu-id="5157a-198">Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilidir).</span><span class="sxs-lookup"><span data-stu-id="5157a-198">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="5157a-199">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-199">Select **Next**.</span></span>
1. <span data-ttu-id="5157a-200">**Roller** ağacında **Web sunucusu (IIS)** öğesini genişletin, **Web sunucusu**' nu genişletin ve ardından **uygulama geliştirme**' yi genişletin.</span><span class="sxs-lookup"><span data-stu-id="5157a-200">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="5157a-201">**WebSocket protokolünü**seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-201">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="5157a-202">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-202">Select **Next**.</span></span>
1. <span data-ttu-id="5157a-203">Ek özellikler gerekmiyorsa, **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-203">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="5157a-204">**Yükle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-204">Select **Install**.</span></span>
1. <span data-ttu-id="5157a-205">Yükleme tamamlandığında sihirbazdan çıkmak için **Kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-205">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="5157a-206">Windows 8 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="5157a-206">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="5157a-207">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="5157a-207">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="5157a-208">**Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="5157a-208">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5157a-209">Şu düğümleri açın: **Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="5157a-209">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="5157a-210">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-210">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5157a-211">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-211">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="5157a-212">Node.js üzerinde socket.io kullanırken WebSocket 'i devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="5157a-212">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="5157a-213">[Node.js](https://nodejs.org/)üzerinde [Socket.io](https://socket.io/) ' de WebSocket DESTEĞINI kullanıyorsanız, varsayılan IIS WebSocket modülünü `webSocket` *web.config* veya *applicationHost.config*öğesini kullanarak devre dışı bırakın. Bu adım gerçekleştirilmemişse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="5157a-213">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="5157a-214">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="5157a-214">Sample app</span></span>

<span data-ttu-id="5157a-215">Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir Echo uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="5157a-215">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="5157a-216">WebSocket bağlantısı yapan bir Web sayfasına sahiptir ve sunucu istemciye geri aldığı tüm iletileri daha sonra sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="5157a-216">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="5157a-217">Uygulamayı bir komut isteminden çalıştırın (IIS Express ile Visual Studio 'dan çalışacak şekilde ayarlanmamış) ve adresine gidin http://localhost:5000 .</span><span class="sxs-lookup"><span data-stu-id="5157a-217">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="5157a-218">Web sayfası, sol üstteki bağlantı durumunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="5157a-218">The web page shows the connection status in the upper left:</span></span>

![Web sayfasının ilk durumu](websockets/_static/start.png)

<span data-ttu-id="5157a-220">Gösterilen URL 'ye WebSocket isteği göndermek için **Bağlan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-220">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="5157a-221">Bir sınama iletisi girin ve **Gönder**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-221">Enter a test message and select **Send**.</span></span> <span data-ttu-id="5157a-222">İşiniz bittiğinde **yuvayı kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5157a-222">When done, select **Close Socket**.</span></span> <span data-ttu-id="5157a-223">**Iletişim günlüğü** bölümünde her açık, gönder ve Kapat eylemi gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5157a-223">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Web sayfasının ilk durumu](websockets/_static/end.png)

