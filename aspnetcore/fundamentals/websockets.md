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
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="a4f7f-103">ASP.NET Core desteği WebSockets</span><span class="sxs-lookup"><span data-stu-id="a4f7f-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="a4f7f-104">[Tom Dykstra](https://github.com/tdykstra) ve [Andrew Stanton-nurte](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a4f7f-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="a4f7f-105">Bu makalede, ASP.NET Core ' de WebSockets ile çalışmaya başlama açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="a4f7f-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)), TCP bağlantıları üzerinden iki yönlü kalıcı iletişim kanalları sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="a4f7f-107">Bu, sohbet, pano ve oyun uygulamaları gibi hızlı, gerçek zamanlı iletişimden faydalanabilir uygulamalarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="a4f7f-108">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a4f7f-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="a4f7f-109">[Nasıl çalıştırılır?](#sample-app)</span><span class="sxs-lookup"><span data-stu-id="a4f7f-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="a4f7f-110">[ASP.NET Core SignalR ](xref:signalr/introduction) , uygulamalara gerçek zamanlı Web işlevselliği eklemeyi kolaylaştıran bir kitaplıktır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="a4f7f-111">Mümkün olduğunda WebSockets kullanır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="a4f7f-112">Çoğu uygulama için SignalR Ham WebSockets üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="a4f7f-113">SignalR WebSockets ' nin kullanılamadığı ortamlar için taşıma geri dönüşü sağlar.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="a4f7f-114">Ayrıca temel bir uzak yordam çağrısı uygulama modeli sağlar.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="a4f7f-115">Ve çoğu senaryoda SignalR Ham WebSockets kullanmaya kıyasla önemli bir performans olumsuz yanı yoktur.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="a4f7f-116">Bazı uygulamalarda, [.net 'Teki GRPC](xref:grpc/index) , WebSockets için bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4f7f-117">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="a4f7f-117">Prerequisites</span></span>

* <span data-ttu-id="a4f7f-118">ASP.NET Core destekleyen herhangi bir işletim sistemi:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="a4f7f-119">Windows 7/Windows Server 2008 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a4f7f-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="a4f7f-120">Linux</span><span class="sxs-lookup"><span data-stu-id="a4f7f-120">Linux</span></span>
  * <span data-ttu-id="a4f7f-121">macOS</span><span class="sxs-lookup"><span data-stu-id="a4f7f-121">macOS</span></span>  
* <span data-ttu-id="a4f7f-122">Uygulama IIS ile Windows üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="a4f7f-123">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a4f7f-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="a4f7f-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="a4f7f-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="a4f7f-125">WebSockets etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-125">WebSockets must be enabled.</span></span> <span data-ttu-id="a4f7f-126">Bkz. [IIS/IIS Express desteği](#iisiis-express-support) bölümü.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="a4f7f-127">Uygulama [HTTP.sys](xref:fundamentals/servers/httpsys)üzerinde çalışıyorsa:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="a4f7f-128">Windows 8/Windows Server 2012 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="a4f7f-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="a4f7f-129">Desteklenen tarayıcılar için bkz https://caniuse.com/#feat=websockets ..</span><span class="sxs-lookup"><span data-stu-id="a4f7f-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="a4f7f-130">Ara yazılımı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a4f7f-130">Configure the middleware</span></span>

<span data-ttu-id="a4f7f-131">WebSockets ara yazılımını `Configure` sınıfının yöntemine ekleyin `Startup` :</span><span class="sxs-lookup"><span data-stu-id="a4f7f-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a4f7f-132">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-132">The following settings can be configured:</span></span>

* <span data-ttu-id="a4f7f-133">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="a4f7f-134">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a4f7f-135">Aşağıdaki ayarlar yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-135">The following settings can be configured:</span></span>

* <span data-ttu-id="a4f7f-136">`KeepAliveInterval` -Proxy 'lerin bağlantının açık kalmasını sağlamak için istemciye "ping" çerçeveleri gönderme sıklığı.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="a4f7f-137">Varsayılan değer iki dakikadır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-137">The default is two minutes.</span></span>
* <span data-ttu-id="a4f7f-138">`AllowedOrigins` -WebSocket istekleri için izin verilen kaynak üst bilgi değerleri listesi.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="a4f7f-139">Varsayılan olarak, tüm kaynaklardan izin verilir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-139">By default, all origins are allowed.</span></span> <span data-ttu-id="a4f7f-140">Ayrıntılar için aşağıdaki "WebSocket kaynak kısıtlaması" başlığına bakın.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="a4f7f-141">WebSocket isteklerini kabul et</span><span class="sxs-lookup"><span data-stu-id="a4f7f-141">Accept WebSocket requests</span></span>

<span data-ttu-id="a4f7f-142">Daha sonra istek yaşam döngüsünün (daha sonra `Configure` yöntemde veya bir eylem yönteminde daha sonra) bir yerde, bir WebSocket isteği olup olmadığını denetleyin ve WebSocket isteğini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="a4f7f-143">Aşağıdaki örnek daha sonra `Configure` yönteminde verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="a4f7f-144">Herhangi bir URL 'de bir WebSocket isteği gelebilir, ancak bu örnek kod yalnızca için istekleri kabul eder `/ws` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="a4f7f-145">WebSocket kullanırken, bağlantı süresince ara yazılım işlem hattını çalışır durumda tutmanız **gerekir** .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="a4f7f-146">Ara yazılım ardışık düzeni bittikten sonra bir WebSocket iletisi göndermeye veya almaya çalışırsanız, aşağıdaki gibi bir özel durum alabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="a4f7f-147">WebSocket 'e veri yazmak için bir arka plan hizmeti kullanıyorsanız, ara yazılım ardışık düzenini çalışır durumda tutmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="a4f7f-148">Bunu kullanarak yapın <xref:System.Threading.Tasks.TaskCompletionSource%601> .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="a4f7f-149">`TaskCompletionSource`' İ arka plan hizmetinize geçirin ve WebSocket ile bitirdiğinizde bu hizmete çağrı yapın <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="a4f7f-150">Ardından `await` , <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> Aşağıdaki örnekte gösterildiği gibi istek sırasında özelliği:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="a4f7f-151">WebSocket kapalı özel durumu bir eylem yönteminden çok yakında döndürüldükten sonra da gerçekleşebilir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="a4f7f-152">Bir eylem yönteminde yuva kabul edilirken, işlem yönteminden dönmeden önce yuva kullanan kodun tamamlanmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="a4f7f-153">`Task.Wait` `Task.Result` Önemli iş parçacığı sorunlarına neden olabileceği için, yuvanın tamamlanmasını beklemek için hiçbir şekilde, veya benzer engelleme çağrılarını kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="a4f7f-154">Her zaman kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="a4f7f-155">İleti alma ve gönderme</span><span class="sxs-lookup"><span data-stu-id="a4f7f-155">Send and receive messages</span></span>

<span data-ttu-id="a4f7f-156">`AcceptWebSocketAsync`Yöntemi, TCP bağlantısını bir WebSocket bağlantısıyla yükseltir ve bir [WebSocket](/dotnet/core/api/system.net.websockets.websocket) nesnesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="a4f7f-157">`WebSocket`İleti göndermek ve almak için nesnesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="a4f7f-158">WebSocket isteğini kabul eden daha önce gösterilen kod, `WebSocket` nesneyi bir `Echo` yönteme geçirir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="a4f7f-159">Kod bir ileti alır ve hemen aynı iletiyi geri gönderir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="a4f7f-160">İletiler, istemci bağlantıyı kapatana kadar bir döngüde gönderilir ve alınır:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="a4f7f-161">Döngüye başlamadan önce WebSocket bağlantısı kabul edildiğinde, ara yazılım ardışık düzeni sona erer.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="a4f7f-162">Yuva kapatıldıktan sonra işlem hattı kaldırımları.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="a4f7f-163">Diğer bir deyişle, WebSocket kabul edildiğinde isteğin işlem hattında ileri doğru hareket ettirilmesi durduruluyor.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="a4f7f-164">Döngü bittiğinde ve yuva kapalıyken, istek ardışık düzeni yedekler.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="a4f7f-165">İstemci bağlantısı kesilen işleme</span><span class="sxs-lookup"><span data-stu-id="a4f7f-165">Handle client disconnects</span></span>

<span data-ttu-id="a4f7f-166">İstemci bağlantı kaybı nedeniyle bağlantısı kesildiğinde sunucu otomatik olarak bilgilendirilmedi.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="a4f7f-167">Sunucu, yalnızca istemci gönderirse, internet bağlantısı kaybedilmişse gerçekleştirilemez bir bağlantı kesme iletisi alır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="a4f7f-168">Bu durumda bazı işlemleri gerçekleştirmek istiyorsanız, belirli bir zaman penceresinde istemciden hiçbir şey alınmadığında bir zaman aşımı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="a4f7f-169">İstemci her zaman ileti göndermiyor ve bağlantı boşta kaldığı için zaman aşımına uğramasını istemiyorsanız, istemcinin her X saniyede bir ping iletisi göndermesi için bir Zamanlayıcı kullanmasını sağlamak için bir Zamanlayıcı kullanmasını sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="a4f7f-170">Sunucusunda, bir ileti \* öncekinden sonra 2 X saniye içinde gelmediyse, bağlantıyı sonlandırın ve istemcinin bağlantısının kesildiğini bildirin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="a4f7f-171">Beklenen zaman aralığının iki kez, ping iletisini tutabilecek Ağ gecikmeleri için ek süre kalmasını bekleyin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="a4f7f-172">WebSocket kaynak kısıtlaması</span><span class="sxs-lookup"><span data-stu-id="a4f7f-172">WebSocket origin restriction</span></span>

<span data-ttu-id="a4f7f-173">CORS tarafından sunulan korumalar WebSockets için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="a4f7f-174">Tarayıcılar şunları **desteklemez**:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-174">Browsers do **not**:</span></span>

* <span data-ttu-id="a4f7f-175">CORS ön uçuş istekleri gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="a4f7f-176">`Access-Control`WebSocket istekleri yapılırken üst bilgilerde belirtilen kısıtlamalara saygı.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="a4f7f-177">Ancak, tarayıcılar `Origin` WebSocket istekleri verirken üstbilgiyi gönderir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="a4f7f-178">Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="a4f7f-179">Sunucunuzu "" üzerinde barındırıyorsanız https://server.com ve istemcinizi "" üzerinde barındırıyorsanız https://client.com , https://client.com `AllowedOrigins` doğrulamak için WebSockets listesine "" ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="a4f7f-180">`Origin`Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="a4f7f-181">Bu üst bilgileri kimlik doğrulama mekanizması **olarak kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="a4f7f-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="a4f7f-182">IIS/IIS Express desteği</span><span class="sxs-lookup"><span data-stu-id="a4f7f-182">IIS/IIS Express support</span></span>

<span data-ttu-id="a4f7f-183">IIS/IIS Express 8 veya üzeri ile Windows Server 2012 veya üzeri ve Windows 8 veya üzeri, WebSocket protokolü için destek içerir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="a4f7f-184">IIS Express kullanılırken WebSockets her zaman etkindir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="a4f7f-185">IIS üzerinde WebSockets etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="a4f7f-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="a4f7f-186">Windows Server 2012 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="a4f7f-187">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="a4f7f-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="a4f7f-188">**Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="a4f7f-189">**Rol tabanlı veya özellik tabanlı yükleme**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="a4f7f-190">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-190">Select **Next**.</span></span>
1. <span data-ttu-id="a4f7f-191">Uygun sunucuyu seçin (yerel sunucu varsayılan olarak seçilidir).</span><span class="sxs-lookup"><span data-stu-id="a4f7f-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="a4f7f-192">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-192">Select **Next**.</span></span>
1. <span data-ttu-id="a4f7f-193">**Roller** ağacında **Web sunucusu (IIS)** öğesini genişletin, **Web sunucusu**' nu genişletin ve ardından **uygulama geliştirme**' yi genişletin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="a4f7f-194">**WebSocket protokolünü** seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="a4f7f-195">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-195">Select **Next**.</span></span>
1. <span data-ttu-id="a4f7f-196">Ek özellikler gerekmiyorsa, **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="a4f7f-197">**Yükle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-197">Select **Install**.</span></span>
1. <span data-ttu-id="a4f7f-198">Yükleme tamamlandığında sihirbazdan çıkmak için **Kapat** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="a4f7f-199">Windows 8 veya sonraki sürümlerde WebSocket protokolü desteğini etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="a4f7f-200">IIS Express kullanılırken bu adımlar gerekli değildir</span><span class="sxs-lookup"><span data-stu-id="a4f7f-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="a4f7f-201">**Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).</span><span class="sxs-lookup"><span data-stu-id="a4f7f-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="a4f7f-202">Şu düğümleri açın: **Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özellikleri**.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="a4f7f-203">**WebSocket protokolü** özelliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="a4f7f-204">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="a4f7f-205">Node.js üzerinde socket.io kullanırken WebSocket 'i devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="a4f7f-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="a4f7f-206">[Node.js](https://nodejs.org/)üzerinde [Socket.io](https://socket.io/) ' de WebSocket DESTEĞINI kullanıyorsanız, varsayılan IIS WebSocket modülünü `webSocket` *web.config* veya *applicationHost.config* öğesini kullanarak devre dışı bırakın. Bu adım gerçekleştirilmemişse, IIS WebSocket modülü Node.js ve uygulama yerine WebSocket iletişimini işlemeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="a4f7f-207">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="a4f7f-207">Sample app</span></span>

<span data-ttu-id="a4f7f-208">Bu makaleye eşlik eden [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) bir Echo uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="a4f7f-209">WebSocket bağlantısı yapan bir Web sayfasına sahiptir ve sunucu istemciye geri aldığı tüm iletileri daha sonra sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="a4f7f-210">Örnek uygulama, IIS Express ile Visual Studio 'dan çalışacak şekilde yapılandırılmamış, bu nedenle uygulamayı ile bir komut kabuğunda çalıştırın ve ' [`dotnet run`](/dotnet/core/tools/dotnet-run) a bir tarayıcıda gidin `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="a4f7f-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="a4f7f-211">Web sayfası bağlantı durumunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="a4f7f-211">The webpage shows the connection status:</span></span>

![WebSockets bağlantısından önce Web sayfasının ilk durumu](websockets/_static/start.png)

<span data-ttu-id="a4f7f-213">Gösterilen URL 'ye WebSocket isteği göndermek için **Bağlan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="a4f7f-214">Bir sınama iletisi girin ve **Gönder**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="a4f7f-215">İşiniz bittiğinde **yuvayı kapat**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="a4f7f-216">**Iletişim günlüğü** bölümünde her açık, gönder ve Kapat eylemi gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="a4f7f-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![WebSockets bağlantısı ve test iletileri gönderildikten ve alındıktan sonra Web sayfasının son durumu](websockets/_static/end.png)
