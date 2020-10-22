---
title: SignalRJavaScript istemcisi ASP.NET Core
author: bradygaster
description: JavaScript istemcisine ASP.NET Core genel bakış SignalR .
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
uid: signalr/javascript-client
ms.openlocfilehash: 6f611e56ec62ad7aea8a93e4761e1f67d0f76574
ms.sourcegitcommit: fad0cd264c9d07a48a8c6ba1690807e0f8728898
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379462"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="599b6-103">SignalRJavaScript istemcisi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="599b6-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="599b6-104">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="599b6-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="599b6-105">SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="599b6-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="599b6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="599b6-107">SignalRİstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="599b6-107">Install the SignalR client package</span></span>

<span data-ttu-id="599b6-108">SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="599b6-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="599b6-109">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="599b6-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="599b6-110">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="599b6-110">Install with npm</span></span>

<span data-ttu-id="599b6-111">Visual Studio için kök klasörde, **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="599b6-112">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="599b6-113">NPM \*node_modules \\ @microsoft\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="599b6-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="599b6-114">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="599b6-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="599b6-115">*signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="599b6-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="599b6-116">SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` .</span><span class="sxs-lookup"><span data-stu-id="599b6-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="599b6-117">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="599b6-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="599b6-118">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="599b6-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="599b6-119">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="599b6-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="599b6-120">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="599b6-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="599b6-121">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="599b6-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="599b6-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="599b6-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="599b6-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="599b6-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="599b6-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="599b6-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="599b6-125">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="599b6-125">Install with LibMan</span></span>

<span data-ttu-id="599b6-126">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="599b6-127">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="599b6-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="599b6-128">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="599b6-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="599b6-129">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="599b6-129">Connect to a hub</span></span>

<span data-ttu-id="599b6-130">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="599b6-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="599b6-131">Hub 'ın adı büyük/küçük harfe duyarlıdır:</span><span class="sxs-lookup"><span data-stu-id="599b6-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="599b6-132">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="599b6-132">Cross-origin connections</span></span>

<span data-ttu-id="599b6-133">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="599b6-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="599b6-134">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="599b6-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="599b6-135">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="599b6-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="599b6-136">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="599b6-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="599b6-137">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="599b6-137">Call hub methods from the client</span></span>

<span data-ttu-id="599b6-138">JavaScript istemcileri, [Hubconnection](/javascript/api/%40microsoft/signalr/hubconnection)'un [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="599b6-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="599b6-139">`invoke`Yöntemi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="599b6-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="599b6-140">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="599b6-140">The name of the hub method.</span></span>
* <span data-ttu-id="599b6-141">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="599b6-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="599b6-142">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="599b6-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="599b6-143">`invoke`Hub yönteminin `user` ve bağımsız değişkenlerine eşlemek için geçirilen ikinci ve üçüncü bağımsız değişkenler `message` :</span><span class="sxs-lookup"><span data-stu-id="599b6-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="599b6-144">Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="599b6-145">Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="599b6-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="599b6-146">`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="599b6-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="599b6-147">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="599b6-148">Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="599b6-149">`async` `await` `Promise` `then` `catch` Bu durumları işlemek için ve ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="599b6-150">JavaScript istemcileri Ayrıca, ' ın [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırabilir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="599b6-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="599b6-151">Yönteminden farklı olarak `invoke` , `send` Yöntem sunucudan bir yanıt beklemez.</span><span class="sxs-lookup"><span data-stu-id="599b6-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="599b6-152">`send`Yöntemi bir JavaScript döndürür `Promise` .</span><span class="sxs-lookup"><span data-stu-id="599b6-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="599b6-153">`Promise`İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="599b6-154">İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="599b6-155">`async` `await` `Promise` `then` `catch` Bu durumları işlemek için ve ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="599b6-156">Kullanmak `send` Sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="599b6-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="599b6-157">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="599b6-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="599b6-158">Hub 'dan istemci yöntemlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="599b6-158">Call client methods from the hub</span></span>

<span data-ttu-id="599b6-159">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="599b6-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="599b6-160">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="599b6-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="599b6-161">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="599b6-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="599b6-162">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="599b6-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="599b6-163">Bağımsız değişken adları şunlardır `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="599b6-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="599b6-164">Yukarıdaki kod, `connection.on` sunucu tarafı kodu yöntemi kullanarak çağırdığında çalıştırılır <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="599b6-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="599b6-165">SignalR ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="599b6-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="599b6-166">En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="599b6-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="599b6-167">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="599b6-168">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="599b6-168">Error handling and logging</span></span>

<span data-ttu-id="599b6-169">`try` `catch` `async` `await` `Promise` `catch` İstemci tarafı hatalarını işlemek için ve ile ve kullanın metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="599b6-170">`console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="599b6-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="599b6-171">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="599b6-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="599b6-172">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="599b6-173">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="599b6-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="599b6-174">`signalR.LogLevel.Error`: Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="599b6-175">`Error`Yalnızca iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="599b6-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="599b6-176">`signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="599b6-177">Günlükler `Warning` ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="599b6-178">`signalR.LogLevel.Information`: Hata olmayan durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="599b6-179">Günlükler `Information` , `Warning` ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="599b6-180">`signalR.LogLevel.Trace`: Trace iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="599b6-181">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="599b6-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="599b6-182">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="599b6-183">İletiler tarayıcı konsoluna kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="599b6-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="599b6-184">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="599b6-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="599b6-185">Otomatik olarak yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="599b6-185">Automatically reconnect</span></span>

<span data-ttu-id="599b6-186">İçin JavaScript istemcisi, SignalR `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="599b6-187">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="599b6-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="599b6-188">Herhangi bir parametre olmadan, `withAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="599b6-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="599b6-189">Yeniden bağlanma girişimlerini başlatmadan önce, durumuna `HubConnection` geçiş yapmak `HubConnectionState.Reconnecting` yerine geri çağırmaları harekete geçer ve `onreconnecting` `Disconnected` `onclose` `HubConnection` Otomatik yeniden bağlanma yapılandırması olmadan geri çağırmaları tetikleyerek.</span><span class="sxs-lookup"><span data-stu-id="599b6-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="599b6-190">Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="599b6-191">İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="599b6-192">Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="599b6-193">Bağlantı sunucuya tamamen yeni göründüğünden geri aramaya yeni bir verilecek `connectionId` `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="599b6-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="599b6-194">`onreconnected`Geri aramanın `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur.</span><span class="sxs-lookup"><span data-stu-id="599b6-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="599b6-195">`withAutomaticReconnect()` , `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="599b6-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="599b6-196">İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="599b6-197">Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:</span><span class="sxs-lookup"><span data-stu-id="599b6-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="599b6-198">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `withAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="599b6-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="599b6-199">Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="599b6-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="599b6-200">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="599b6-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="599b6-201">İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.</span><span class="sxs-lookup"><span data-stu-id="599b6-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="599b6-202">İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="599b6-203">Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="599b6-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="599b6-204">Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="599b6-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="599b6-205">`nextRetryDelayInMilliseconds` türünde tek bir bağımsız değişken alır `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="599b6-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="599b6-206">,, `RetryContext` `previousRetryCount` `elapsedMilliseconds` Ve `retryReason` sırasıyla bir olan `number` `number` üç özelliğe `Error` sahiptir:.</span><span class="sxs-lookup"><span data-stu-id="599b6-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="599b6-207">İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve `retryReason` bağlantının kaybolmasına neden olan hata olacaktır.</span><span class="sxs-lookup"><span data-stu-id="599b6-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="599b6-208">Her başarısız yeniden deneme denemesinden sonra `previousRetryCount` bir artırılır `elapsedMilliseconds` . Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve `retryReason` son yeniden bağlanma girişiminin başarısız olmasına neden olan hata olur.</span><span class="sxs-lookup"><span data-stu-id="599b6-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="599b6-209">`nextRetryDelayInMilliseconds` sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="599b6-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="599b6-210">Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="599b6-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="599b6-211">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="599b6-211">Manually reconnect</span></span>

<span data-ttu-id="599b6-212">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="599b6-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="599b6-213">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="599b6-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="599b6-214">`start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="599b6-215">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="599b6-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="599b6-216">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="599b6-216">Additional resources</span></span>

* [<span data-ttu-id="599b6-217">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="599b6-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="599b6-218">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="599b6-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="599b6-219">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="599b6-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="599b6-220">Merkezler</span><span class="sxs-lookup"><span data-stu-id="599b6-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="599b6-221">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="599b6-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="599b6-222">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="599b6-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="599b6-223">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="599b6-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="599b6-224">Azure SignalR hizmeti sunucusuz belgeler</span><span class="sxs-lookup"><span data-stu-id="599b6-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="599b6-225">Bağlantı hatalarını giderme</span><span class="sxs-lookup"><span data-stu-id="599b6-225">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="599b6-226">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="599b6-226">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="599b6-227">SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-227">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="599b6-228">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="599b6-228">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="599b6-229">SignalRİstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="599b6-229">Install the SignalR client package</span></span>

<span data-ttu-id="599b6-230">SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="599b6-230">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="599b6-231">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="599b6-231">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="599b6-232">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="599b6-232">Install with npm</span></span>

<span data-ttu-id="599b6-233">Visual Studio kullanıyorsanız, kök klasörde **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-233">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="599b6-234">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-234">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="599b6-235">NPM \*node_modules \\ @aspnet\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="599b6-235">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="599b6-236">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="599b6-236">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="599b6-237">*signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="599b6-237">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="599b6-238">SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` .</span><span class="sxs-lookup"><span data-stu-id="599b6-238">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="599b6-239">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="599b6-239">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="599b6-240">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="599b6-240">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="599b6-241">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="599b6-241">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="599b6-242">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="599b6-242">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="599b6-243">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="599b6-243">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="599b6-244">cdnjs</span><span class="sxs-lookup"><span data-stu-id="599b6-244">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="599b6-245">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="599b6-245">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="599b6-246">unpkg</span><span class="sxs-lookup"><span data-stu-id="599b6-246">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="599b6-247">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="599b6-247">Install with LibMan</span></span>

<span data-ttu-id="599b6-248">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-248">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="599b6-249">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="599b6-249">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="599b6-250">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="599b6-250">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="599b6-251">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="599b6-251">Connect to a hub</span></span>

<span data-ttu-id="599b6-252">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="599b6-252">The following code creates and starts a connection.</span></span> <span data-ttu-id="599b6-253">Hub 'ın adı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="599b6-253">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="599b6-254">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="599b6-254">Cross-origin connections</span></span>

<span data-ttu-id="599b6-255">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="599b6-255">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="599b6-256">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="599b6-256">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="599b6-257">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="599b6-257">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="599b6-258">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="599b6-258">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="599b6-259">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="599b6-259">Call hub methods from client</span></span>

<span data-ttu-id="599b6-260">JavaScript istemcileri, [Hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)'un [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="599b6-260">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="599b6-261">`invoke`Yöntemi iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="599b6-261">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="599b6-262">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="599b6-262">The name of the hub method.</span></span> <span data-ttu-id="599b6-263">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="599b6-263">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="599b6-264">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="599b6-264">Any arguments defined in the hub method.</span></span> <span data-ttu-id="599b6-265">Aşağıdaki örnekte, bağımsız değişken adı `message` .</span><span class="sxs-lookup"><span data-stu-id="599b6-265">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="599b6-266">Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="599b6-266">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="599b6-267">Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-267">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="599b6-268">Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="599b6-268">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="599b6-269">`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="599b6-269">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="599b6-270">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-270">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="599b6-271">Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-271">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="599b6-272">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="599b6-272">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="599b6-273">`send`Yöntemi bir JavaScript döndürür `Promise` .</span><span class="sxs-lookup"><span data-stu-id="599b6-273">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="599b6-274">`Promise`İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="599b6-274">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="599b6-275">İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-275">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="599b6-276">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="599b6-276">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="599b6-277">Kullanmak `send` Sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="599b6-277">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="599b6-278">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="599b6-278">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="599b6-279">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="599b6-279">Call client methods from hub</span></span>

<span data-ttu-id="599b6-280">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40aspnet/signalr/hubconnection#on) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="599b6-280">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="599b6-281">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="599b6-281">The name of the JavaScript client method.</span></span> <span data-ttu-id="599b6-282">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="599b6-282">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="599b6-283">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="599b6-283">Arguments the hub passes to the method.</span></span> <span data-ttu-id="599b6-284">Aşağıdaki örnekte, bağımsız değişken değeri `message` .</span><span class="sxs-lookup"><span data-stu-id="599b6-284">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="599b6-285">Yukarıdaki kod, `connection.on` sunucu tarafı kodu yöntemini kullanarak çağırdığında çalıştırılır <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="599b6-285">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="599b6-286">SignalR ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="599b6-286">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="599b6-287">En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="599b6-287">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="599b6-288">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-288">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="599b6-289">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="599b6-289">Error handling and logging</span></span>

<span data-ttu-id="599b6-290">`catch` `start` İstemci tarafı hatalarını işlemek için yönteminin sonuna bir yöntemi zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="599b6-290">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="599b6-291">`console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-291">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="599b6-292">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="599b6-292">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="599b6-293">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-293">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="599b6-294">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="599b6-294">Available log levels are as follows:</span></span>

* <span data-ttu-id="599b6-295">`signalR.LogLevel.Error`: Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-295">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="599b6-296">`Error`Yalnızca iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="599b6-296">Logs `Error` messages only.</span></span>
* <span data-ttu-id="599b6-297">`signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-297">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="599b6-298">Günlükler `Warning` ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="599b6-298">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="599b6-299">`signalR.LogLevel.Information`: Hata olmayan durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-299">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="599b6-300">Günlükler `Information` , `Warning` ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-300">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="599b6-301">`signalR.LogLevel.Trace`: Trace iletileri.</span><span class="sxs-lookup"><span data-stu-id="599b6-301">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="599b6-302">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="599b6-302">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="599b6-303">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="599b6-303">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="599b6-304">İletiler tarayıcı konsoluna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="599b6-304">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="599b6-305">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="599b6-305">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="599b6-306">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="599b6-306">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="599b6-307">3,0 ' den önce, için JavaScript istemcisi SignalR otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="599b6-307">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="599b6-308">İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="599b6-308">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="599b6-309">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="599b6-309">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="599b6-310">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="599b6-310">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="599b6-311">`start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="599b6-311">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="599b6-312">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="599b6-312">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="599b6-313">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="599b6-313">Additional resources</span></span>

* [<span data-ttu-id="599b6-314">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="599b6-314">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="599b6-315">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="599b6-315">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="599b6-316">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="599b6-316">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="599b6-317">Merkezler</span><span class="sxs-lookup"><span data-stu-id="599b6-317">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="599b6-318">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="599b6-318">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="599b6-319">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="599b6-319">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="599b6-320">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="599b6-320">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="599b6-321">Azure SignalR hizmeti sunucusuz belgeler</span><span class="sxs-lookup"><span data-stu-id="599b6-321">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
