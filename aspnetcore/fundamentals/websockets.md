---
title: ASP.NET Core desteği WebSockets
author: rick-anderson
description: ASP.NET Core 'de WebSockets kullanmaya başlama hakkında bilgi edinin.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/websockets
ms.openlocfilehash: 83a41d503b2d56bca3f1bac14eeb9d54a8257642
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057784"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="5d64a-103">ASP.NET Core desteği WebSockets</span><span class="sxs-lookup"><span data-stu-id="5d64a-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="5d64a-104">[Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5d64a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="5d64a-105">Bu makalede, ASP.NET Core ' de WebSockets ile çalışmaya başlama açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="5d64a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)), TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="5d64a-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="5d64a-107">Bu, sohbet, pano ve oyun uygulamaları gibi hızlı, gerçek zamanlı iletişimden faydalanabilir uygulamalarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="5d64a-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="5d64a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="5d64a-109">[Nasıl çalıştırılır?](#sample-app)</span><span class="sxs-lookup"><span data-stu-id="5d64a-109">[How to run](#sample-app).</span></span>

## :::no-loc(SignalR):::

<span data-ttu-id="5d64a-110">[ASP.NET Core :::no-loc(SignalR)::: ](xref:signalr/introduction) , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-110">[ASP.NET Core :::no-loc(SignalR):::](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="5d64a-111">Mümkün olduğunda WebSockets kullanır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="5d64a-112">Çoğu uygulama için :::no-loc(SignalR)::: Ham WebSockets üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-112">For most applications, we recommend :::no-loc(SignalR)::: over raw WebSockets.</span></span> <span data-ttu-id="5d64a-113">:::no-loc(SignalR)::: WebSockets ' nin kullanılamadığı ortamlar için taşıma geri dönüşü sağlar.</span><span class="sxs-lookup"><span data-stu-id="5d64a-113">:::no-loc(SignalR)::: provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="5d64a-114">Ayrıca temel bir uzak yordam çağrısı uygulama modeli sağlar.</span><span class="sxs-lookup"><span data-stu-id="5d64a-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="5d64a-115">Ve çoğu senaryoda :::no-loc(SignalR)::: Ham WebSockets kullanmaya kıyasla önemli bir performans olumsuz yanı yoktur.</span><span class="sxs-lookup"><span data-stu-id="5d64a-115">And in most scenarios, :::no-loc(SignalR)::: has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="5d64a-116">Bazı uygulamalarda, [.net 'Teki GRPC](xref:grpc/index) , WebSockets için bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="5d64a-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5d64a-117">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="5d64a-117">Prerequisites</span></span>

* <span data-ttu-id="5d64a-118">ASP.NET Core destekleyen herhangi bir işletim sistemi:</span><span class="sxs-lookup"><span data-stu-id="5d64a-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="5d64a-119">Windows 7/Windows Server 2008 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5d64a-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="5d64a-120">Linux</span><span class="sxs-lookup"><span data-stu-id="5d64a-120">Linux</span></span>
  * <span data-ttu-id="5d64a-121">macOS</span><span class="sxs-lookup"><span data-stu-id="5d64a-121">macOS</span></span>  
* <span data-ttu-id="5d64a-122">Uygulama IIS ile Windows üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5d64a-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="5d64a-123">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5d64a-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="5d64a-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="5d64a-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="5d64a-125">WebSockets etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-125">WebSockets must be enabled.</span></span> <span data-ttu-id="5d64a-126">Bkz. [IIS/IIS Express desteği](#iisiis-express-support) bölümü.</span><span class="sxs-lookup"><span data-stu-id="5d64a-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="5d64a-127">Uygulama [HTTP.sys](xref:fundamentals/servers/httpsys)üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="5d64a-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="5d64a-128">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="5d64a-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="5d64a-129">Desteklenen tarayıcılar için bkz https://caniuse.com/#feat=websockets ..</span><span class="sxs-lookup"><span data-stu-id="5d64a-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="5d64a-130">Ara yazılımı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5d64a-130">Configure the middleware</span></span>

<span data-ttu-id="5d64a-131">WebSockets ara yazılımını `Configure` sınıfının yöntemine ekleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="5d64a-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5d64a-132">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="5d64a-132">The following settings can be configured:</span></span>

* <span data-ttu-id="5d64a-133">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="5d64a-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="5d64a-134">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-134">The default is two minutes.</span></span>
* <span data-ttu-id="5d64a-135">`ReceiveBufferSize` -Verileri almak için kullanılan arabelleğin boyutu.</span><span class="sxs-lookup"><span data-stu-id="5d64a-135">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="5d64a-136">Gelişmiş kullanıcıların, verilerin boyutuna bağlı olarak performans ayarlaması için bunu değiştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-136">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="5d64a-137">Varsayılan değer 4 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-137">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5d64a-138">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="5d64a-138">The following settings can be configured:</span></span>

* <span data-ttu-id="5d64a-139">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="5d64a-139">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="5d64a-140">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-140">The default is two minutes.</span></span>
* <span data-ttu-id="5d64a-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> -Verileri almak için kullanılan arabelleğin boyutu.</span><span class="sxs-lookup"><span data-stu-id="5d64a-141"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="5d64a-142">Gelişmiş kullanıcıların, verilerin boyutuna bağlı olarak performans ayarlaması için bunu değiştirmesi gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-142">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="5d64a-143">Varsayılan değer 4 KB 'tır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-143">The default is 4 KB.</span></span>
* <span data-ttu-id="5d64a-144">`AllowedOrigins` -WebSocket istekleri için izin verilen kaynak üst bilgi değerleri listesi.</span><span class="sxs-lookup"><span data-stu-id="5d64a-144">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="5d64a-145">Varsayılan olarak, tüm kaynaklardan izin verilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-145">By default, all origins are allowed.</span></span> <span data-ttu-id="5d64a-146">Ayrıntılar için aşağıdaki "WebSocket kaynak kısıtlaması" başlığına bakın.</span><span class="sxs-lookup"><span data-stu-id="5d64a-146">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="5d64a-147">WebSocket isteklerini kabul et</span><span class="sxs-lookup"><span data-stu-id="5d64a-147">Accept WebSocket requests</span></span>

<span data-ttu-id="5d64a-148">Daha sonra istek yaşam döngüsünün (daha sonra `Configure` yöntemde veya bir eylem yönteminde daha sonra) bir yerde, bir WebSocket isteği olup olmadığını denetleyin ve WebSocket isteğini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-148">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="5d64a-149">Aşağıdaki örnek daha sonra `Configure` yönteminde verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="5d64a-149">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="5d64a-150">Herhangi bir URL 'de bir WebSocket isteği gelebilir, ancak bu örnek kod yalnızca için istekleri kabul eder `/ws` .</span><span class="sxs-lookup"><span data-stu-id="5d64a-150">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="5d64a-151">WebSocket kullanırken, bağlantı süresince ara yazılım işlem hattını çalışır durumda tutmanız **gerekir** .</span><span class="sxs-lookup"><span data-stu-id="5d64a-151">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="5d64a-152">Ara yazılım ardışık düzeni bittikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5d64a-152">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="5d64a-153">WebSocket 'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık düzenini çalışır durumda tutmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-153">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="5d64a-154">Bunu kullanarak yapın <xref:System.Threading.Tasks.TaskCompletionSource%601> .</span><span class="sxs-lookup"><span data-stu-id="5d64a-154">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="5d64a-155">`TaskCompletionSource`' İ arka plan hizmetinize geçirin ve WebSocket ile bitirdiğinizde bu hizmete çağrı yapın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> .</span><span class="sxs-lookup"><span data-stu-id="5d64a-155">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="5d64a-156">Ardından `await` , <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> Aşağıdaki örnekte gösterildiği gibi istek sırasında özelliği:</span><span class="sxs-lookup"><span data-stu-id="5d64a-156">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="5d64a-157">WebSocket kapalı özel durumu bir eylem yönteminden çok yakında döndürüldükten sonra da gerçekleşebilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-157">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="5d64a-158">Bir eylem yönteminde yuva kabul edilirken, işlem yönteminden dönmeden önce yuva kullanan kodun tamamlanmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-158">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="5d64a-159">`Task.Wait` `Task.Result` Önemli iş parçacığı sorunlarına neden olabileceği için, yuvanın tamamlanmasını beklemek için hiçbir şekilde, veya benzer engelleme çağrılarını kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5d64a-159">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="5d64a-160">Her zaman kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="5d64a-160">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="5d64a-161">İleti alma ve gönderme</span><span class="sxs-lookup"><span data-stu-id="5d64a-161">Send and receive messages</span></span>

<span data-ttu-id="5d64a-162">`AcceptWebSocketAsync`Yöntemi, TCP bağlantısını bir WebSocket bağlantısıyla yükseltir ve bir [WebSocket](/dotnet/core/api/system.net.websockets.websocket) nesnesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="5d64a-162">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="5d64a-163">`WebSocket`İleti göndermek ve almak için nesnesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="5d64a-163">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="5d64a-164">WebSocket isteğini kabul eden daha önce gösterilen kod, `WebSocket` nesneyi bir `Echo` yönteme geçirir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-164">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="5d64a-165">Kod bir ileti alır ve hemen aynı iletiyi geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-165">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="5d64a-166">İletiler, istemci bağlantıyı kapatana kadar bir döngüde gönderilir ve alınır:</span><span class="sxs-lookup"><span data-stu-id="5d64a-166">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="5d64a-167">Döngüye başlamadan önce WebSocket bağlantısı kabul edildiğinde, ara yazılım ardışık düzeni sona erer.</span><span class="sxs-lookup"><span data-stu-id="5d64a-167">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="5d64a-168">Yuva kapatıldıktan sonra işlem hattı kaldırımları.</span><span class="sxs-lookup"><span data-stu-id="5d64a-168">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="5d64a-169">Diğer bir deyişle, WebSocket kabul edildiğinde isteğin işlem hattında ileri doğru hareket ettirilmesi durduruluyor.</span><span class="sxs-lookup"><span data-stu-id="5d64a-169">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="5d64a-170">Döngü bittiğinde ve yuva kapalıyken, istek ardışık düzeni yedekler.</span><span class="sxs-lookup"><span data-stu-id="5d64a-170">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="5d64a-171">İstemci bağlantısı kesilen işleme</span><span class="sxs-lookup"><span data-stu-id="5d64a-171">Handle client disconnects</span></span>

<span data-ttu-id="5d64a-172">İstemci bağlantı kaybı nedeniyle bağlantısı kesildiğinde sunucu otomatik olarak bilgilendirilmedi.</span><span class="sxs-lookup"><span data-stu-id="5d64a-172">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="5d64a-173">Sunucu, yalnızca istemci gönderirse, internet bağlantısı kaybedilmişse gerçekleştirilemez bir bağlantı kesme iletisi alır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-173">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="5d64a-174">Bu durumda bazı işlemleri gerçekleştirmek istiyorsanız, belirli bir zaman penceresinde istemciden hiçbir şey alınmadığında bir zaman aşımı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="5d64a-174">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="5d64a-175">İstemci her zaman ileti göndermiyor ve bağlantı boşta kaldığı için zaman aşımına uğramasını istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermesi için bir Zamanlayıcı kullanmasını sağlamak için bir Zamanlayıcı kullanmasını sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5d64a-175">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="5d64a-176">Sunucusunda, bir ileti \* öncekinden sonra 2 X saniye içinde gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-176">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="5d64a-177">Beklenen zaman aralığının iki kez, ping iletisini tutabilecek Ağ gecikmeleri için ek süre kalmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-177">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="5d64a-178">WebSocket kaynak kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="5d64a-178">WebSocket origin restriction</span></span>

<span data-ttu-id="5d64a-179">CORS tarafından sunulan korumalar WebSockets için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-179">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="5d64a-180">Tarayıcılar şunları **desteklemez** :</span><span class="sxs-lookup"><span data-stu-id="5d64a-180">Browsers do **not** :</span></span>

* <span data-ttu-id="5d64a-181">CORS ön uçuş istekleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-181">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="5d64a-182">`Access-Control`WebSocket istekleri yapılırken üst bilgilerde belirtilen kısıtlamalara saygı.</span><span class="sxs-lookup"><span data-stu-id="5d64a-182">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="5d64a-183">Ancak, tarayıcılar `Origin` WebSocket istekleri verirken üstbilgiyi gönderir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-183">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="5d64a-184">Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-184">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="5d64a-185">Sunucunuzu "" üzerinde barındırıyorsanız https://server.com ve istemcinizi "" üzerinde barındırıyorsanız https://client.com , https://client.com `AllowedOrigins` doğrulamak için WebSockets listesine "" ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-185">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="5d64a-186">`Origin`Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-186">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="5d64a-187">Bu üst bilgileri kimlik doğrulama mekanizması **olarak kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="5d64a-187">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="5d64a-188">IIS/IIS Express desteği</span><span class="sxs-lookup"><span data-stu-id="5d64a-188">IIS/IIS Express support</span></span>

<span data-ttu-id="5d64a-189">IIS/IIS Express 8 veya üzeri ile Windows Server 2012 veya üzeri ve Windows 8 veya üzeri, WebSocket protokolü için destek içerir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-189">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="5d64a-190">IIS Express kullanılırken WebSockets her zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-190">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="5d64a-191">IIS üzerinde WebSockets etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="5d64a-191">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="5d64a-192">Windows Server 2012 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="5d64a-192">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="5d64a-193">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="5d64a-193">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="5d64a-194">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5d64a-194">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span>
1. <span data-ttu-id="5d64a-195">**Rol tabanlı veya özellik tabanlı yükleme** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-195">Select **Role-based or Feature-based Installation** .</span></span> <span data-ttu-id="5d64a-196">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-196">Select **Next** .</span></span>
1. <span data-ttu-id="5d64a-197">Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilidir).</span><span class="sxs-lookup"><span data-stu-id="5d64a-197">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="5d64a-198">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-198">Select **Next** .</span></span>
1. <span data-ttu-id="5d64a-199">**Roller** ağacında **Web sunucusu (IIS)** öğesini genişletin, **Web sunucusu** ' nu genişletin ve ardından **uygulama geliştirme** ' yi genişletin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-199">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server** , and then expand **Application Development** .</span></span>
1. <span data-ttu-id="5d64a-200">**WebSocket protokolünü** seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-200">Select **WebSocket Protocol** .</span></span> <span data-ttu-id="5d64a-201">**İleri** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-201">Select **Next** .</span></span>
1. <span data-ttu-id="5d64a-202">Ek özellikler gerekmiyorsa, **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-202">If additional features aren't needed, select **Next** .</span></span>
1. <span data-ttu-id="5d64a-203">**Yükle** 'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-203">Select **Install** .</span></span>
1. <span data-ttu-id="5d64a-204">Yükleme tamamlandığında sihirbazdan çıkmak için **Kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-204">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="5d64a-205">Windows 8 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="5d64a-205">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="5d64a-206">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="5d64a-206">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="5d64a-207">**Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="5d64a-207">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="5d64a-208">Şu düğümleri açın: **Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özellikleri** .</span><span class="sxs-lookup"><span data-stu-id="5d64a-208">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="5d64a-209">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-209">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="5d64a-210">**Tamam** ’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-210">Select **OK** .</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="5d64a-211">Node.js üzerinde socket.io kullanırken WebSocket 'i devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="5d64a-211">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="5d64a-212">[Node.js](https://nodejs.org/)üzerinde [Socket.io](https://socket.io/) ' de WebSocket DESTEĞINI kullanıyorsanız, varsayılan IIS WebSocket modülünü `webSocket` *web.config* veya *applicationHost.config* öğesini kullanarak devre dışı bırakın. Bu adım gerçekleştirilmemişse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-212">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config* . If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="5d64a-213">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="5d64a-213">Sample app</span></span>

<span data-ttu-id="5d64a-214">Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir Echo uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-214">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="5d64a-215">WebSocket bağlantısı yapan bir Web sayfasına sahiptir ve sunucu istemciye geri aldığı tüm iletileri daha sonra sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="5d64a-215">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="5d64a-216">Uygulamayı bir komut isteminden çalıştırın (IIS Express ile Visual Studio 'dan çalışacak şekilde ayarlanmamış) ve adresine gidin http://localhost:5000 .</span><span class="sxs-lookup"><span data-stu-id="5d64a-216">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="5d64a-217">Web sayfası, sol üstteki bağlantı durumunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="5d64a-217">The web page shows the connection status in the upper left:</span></span>

![Web sayfasının ilk durumu](websockets/_static/start.png)

<span data-ttu-id="5d64a-219">Gösterilen URL 'ye WebSocket isteği göndermek için **Bağlan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-219">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="5d64a-220">Bir sınama iletisi girin ve **Gönder** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-220">Enter a test message and select **Send** .</span></span> <span data-ttu-id="5d64a-221">İşiniz bittiğinde **yuvayı kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="5d64a-221">When done, select **Close Socket** .</span></span> <span data-ttu-id="5d64a-222">**Iletişim günlüğü** bölümünde her açık, gönder ve Kapat eylemi gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="5d64a-222">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Web sayfasının ilk durumu](websockets/_static/end.png)

