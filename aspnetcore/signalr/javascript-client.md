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
ms.openlocfilehash: 359aa2b9e6b7f826d75f10645b7f2b565ab48b7a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847695"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="7b1ab-103">SignalRJavaScript istemcisi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b1ab-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b1ab-104">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="7b1ab-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="7b1ab-105">SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="7b1ab-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b1ab-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="7b1ab-107">SignalRİstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-107">Install the SignalR client package</span></span>

<span data-ttu-id="7b1ab-108">SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="7b1ab-109">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="7b1ab-110">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="7b1ab-110">Install with npm</span></span>

<span data-ttu-id="7b1ab-111">Visual Studio için kök klasörde, **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="7b1ab-112">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="7b1ab-113">NPM \*node_modules \\ @microsoft\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7b1ab-114">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7b1ab-115">*signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="7b1ab-116">SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="7b1ab-117">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="7b1ab-118">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="7b1ab-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="7b1ab-119">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="7b1ab-120">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="7b1ab-121">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="7b1ab-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="7b1ab-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="7b1ab-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="7b1ab-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="7b1ab-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="7b1ab-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="7b1ab-125">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="7b1ab-125">Install with LibMan</span></span>

<span data-ttu-id="7b1ab-126">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="7b1ab-127">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="7b1ab-128">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="7b1ab-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="7b1ab-129">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="7b1ab-129">Connect to a hub</span></span>

<span data-ttu-id="7b1ab-130">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="7b1ab-131">Hub 'ın adı büyük/küçük harfe duyarlıdır:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="7b1ab-132">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="7b1ab-132">Cross-origin connections</span></span>

<span data-ttu-id="7b1ab-133">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="7b1ab-134">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="7b1ab-135">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="7b1ab-136">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="7b1ab-137">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="7b1ab-137">Call hub methods from the client</span></span>

<span data-ttu-id="7b1ab-138">JavaScript istemcileri, [Hubconnection](/javascript/api/%40microsoft/signalr/hubconnection)'un [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="7b1ab-139">`invoke`Yöntemi kabul eder:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="7b1ab-140">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-140">The name of the hub method.</span></span>
* <span data-ttu-id="7b1ab-141">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="7b1ab-142">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="7b1ab-143">`invoke`Hub yönteminin `user` ve bağımsız değişkenlerine eşlemek için geçirilen ikinci ve üçüncü bağımsız değişkenler `message` :</span><span class="sxs-lookup"><span data-stu-id="7b1ab-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="7b1ab-144">Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="7b1ab-145">Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="7b1ab-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="7b1ab-146">`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="7b1ab-147">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="7b1ab-148">Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7b1ab-149">`async` `await` `Promise` `then` `catch` Bu durumları işlemek için ve ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="7b1ab-150">JavaScript istemcileri Ayrıca, ' ın [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırabilir `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="7b1ab-151">Yönteminden farklı olarak `invoke` , `send` Yöntem sunucudan bir yanıt beklemez.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="7b1ab-152">`send`Yöntemi bir JavaScript döndürür `Promise` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="7b1ab-153">`Promise`İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="7b1ab-154">İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7b1ab-155">`async` `await` `Promise` `then` `catch` Bu durumları işlemek için ve ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="7b1ab-156">Kullanmak `send` Sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="7b1ab-157">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="7b1ab-158">Hub 'dan istemci yöntemlerini çağırma</span><span class="sxs-lookup"><span data-stu-id="7b1ab-158">Call client methods from the hub</span></span>

<span data-ttu-id="7b1ab-159">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="7b1ab-160">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="7b1ab-161">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="7b1ab-162">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="7b1ab-163">Bağımsız değişken adları şunlardır `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="7b1ab-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="7b1ab-164">Yukarıdaki kod, `connection.on` sunucu tarafı kodu yöntemi kullanarak çağırdığında çalıştırılır <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="7b1ab-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="7b1ab-165">SignalR ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="7b1ab-166">En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="7b1ab-167">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="7b1ab-168">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="7b1ab-168">Error handling and logging</span></span>

<span data-ttu-id="7b1ab-169">`try` `catch` `async` `await` `Promise` `catch` İstemci tarafı hatalarını işlemek için ve ile ve kullanın metodunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="7b1ab-170">`console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="7b1ab-171">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="7b1ab-172">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="7b1ab-173">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="7b1ab-174">`signalR.LogLevel.Error`: Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="7b1ab-175">`Error`Yalnızca iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="7b1ab-176">`signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="7b1ab-177">Günlükler `Warning` ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7b1ab-178">`signalR.LogLevel.Information`: Hata olmayan durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="7b1ab-179">Günlükler `Information` , `Warning` ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7b1ab-180">`signalR.LogLevel.Trace`: Trace iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="7b1ab-181">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="7b1ab-182">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="7b1ab-183">İletiler tarayıcı konsoluna kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="7b1ab-184">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="7b1ab-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="7b1ab-185">Otomatik olarak yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="7b1ab-185">Automatically reconnect</span></span>

<span data-ttu-id="7b1ab-186">İçin JavaScript istemcisi, SignalR `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="7b1ab-187">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="7b1ab-188">Herhangi bir parametre olmadan, `withAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="7b1ab-189">Yeniden bağlanma girişimlerini başlatmadan önce, durumuna `HubConnection` geçiş yapmak `HubConnectionState.Reconnecting` yerine geri çağırmaları harekete geçer ve `onreconnecting` `Disconnected` `onclose` `HubConnection` Otomatik yeniden bağlanma yapılandırması olmadan geri çağırmaları tetikleyerek.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="7b1ab-190">Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7b1ab-191">İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="7b1ab-192">Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="7b1ab-193">Bağlantı sunucuya tamamen yeni göründüğünden geri aramaya yeni bir verilecek `connectionId` `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="7b1ab-194">`onreconnected`Geri aramanın `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7b1ab-195">`withAutomaticReconnect()` , `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="7b1ab-196">İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="7b1ab-197">Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="7b1ab-198">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `withAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="7b1ab-199">Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="7b1ab-200">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="7b1ab-201">İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7b1ab-202">İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="7b1ab-203">Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="7b1ab-204">Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="7b1ab-205">`nextRetryDelayInMilliseconds` türünde tek bir bağımsız değişken alır `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="7b1ab-206">,, `RetryContext` `previousRetryCount` `elapsedMilliseconds` Ve `retryReason` sırasıyla bir olan `number` `number` üç özelliğe `Error` sahiptir:.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="7b1ab-207">İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve `retryReason` bağlantının kaybolmasına neden olan hata olacaktır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="7b1ab-208">Her başarısız yeniden deneme denemesinden sonra `previousRetryCount` bir artırılır `elapsedMilliseconds` . Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve `retryReason` son yeniden bağlanma girişiminin başarısız olmasına neden olan hata olur.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="7b1ab-209">`nextRetryDelayInMilliseconds` sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="7b1ab-210">Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="7b1ab-211">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="7b1ab-211">Manually reconnect</span></span>

<span data-ttu-id="7b1ab-212">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="7b1ab-213">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="7b1ab-214">`start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="7b1ab-215">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b1ab-216">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7b1ab-216">Additional resources</span></span>

* [<span data-ttu-id="7b1ab-217">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="7b1ab-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="7b1ab-218">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7b1ab-219">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="7b1ab-220">Merkezler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7b1ab-221">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7b1ab-222">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="7b1ab-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="7b1ab-223">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="7b1ab-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="7b1ab-224">Azure SignalR hizmeti sunucusuz belgeler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7b1ab-225">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="7b1ab-225">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="7b1ab-226">SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-226">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="7b1ab-227">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b1ab-227">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="7b1ab-228">SignalRİstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-228">Install the SignalR client package</span></span>

<span data-ttu-id="7b1ab-229">SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-229">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="7b1ab-230">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-230">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="7b1ab-231">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="7b1ab-231">Install with npm</span></span>

<span data-ttu-id="7b1ab-232">Visual Studio kullanıyorsanız, kök klasörde **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-232">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="7b1ab-233">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-233">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="7b1ab-234">NPM \*node_modules \\ @aspnet\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-234">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="7b1ab-235">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-235">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="7b1ab-236">*signalr.js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-236">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="7b1ab-237">SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-237">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="7b1ab-238">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-238">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="7b1ab-239">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="7b1ab-239">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="7b1ab-240">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-240">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="7b1ab-241">Örnek:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-241">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="7b1ab-242">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-242">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="7b1ab-243">cdnjs</span><span class="sxs-lookup"><span data-stu-id="7b1ab-243">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="7b1ab-244">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="7b1ab-244">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="7b1ab-245">unpkg</span><span class="sxs-lookup"><span data-stu-id="7b1ab-245">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="7b1ab-246">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="7b1ab-246">Install with LibMan</span></span>

<span data-ttu-id="7b1ab-247">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-247">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="7b1ab-248">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-248">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="7b1ab-249">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="7b1ab-249">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="7b1ab-250">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="7b1ab-250">Connect to a hub</span></span>

<span data-ttu-id="7b1ab-251">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-251">The following code creates and starts a connection.</span></span> <span data-ttu-id="7b1ab-252">Hub 'ın adı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-252">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="7b1ab-253">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="7b1ab-253">Cross-origin connections</span></span>

<span data-ttu-id="7b1ab-254">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-254">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="7b1ab-255">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-255">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="7b1ab-256">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-256">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="7b1ab-257">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-257">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7b1ab-258">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="7b1ab-258">Call hub methods from client</span></span>

<span data-ttu-id="7b1ab-259">JavaScript istemcileri, [Hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)'un [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-259">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="7b1ab-260">`invoke`Yöntemi iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-260">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="7b1ab-261">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-261">The name of the hub method.</span></span> <span data-ttu-id="7b1ab-262">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-262">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="7b1ab-263">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-263">Any arguments defined in the hub method.</span></span> <span data-ttu-id="7b1ab-264">Aşağıdaki örnekte, bağımsız değişken adı `message` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-264">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="7b1ab-265">Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-265">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="7b1ab-266">Hub yöntemlerinin bir istemciden çağrılması yalnızca Azure SignalR hizmeti *varsayılan* modda kullanılırken desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-266">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="7b1ab-267">Daha fazla bilgi için bkz. [sık sorulan sorular (Azure-SignalR GitHub deposu)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="7b1ab-267">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="7b1ab-268">`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-268">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="7b1ab-269">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-269">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="7b1ab-270">Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-270">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7b1ab-271">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-271">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="7b1ab-272">`send`Yöntemi bir JavaScript döndürür `Promise` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-272">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="7b1ab-273">`Promise`İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-273">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="7b1ab-274">İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-274">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="7b1ab-275">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-275">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="7b1ab-276">Kullanmak `send` Sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-276">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="7b1ab-277">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-277">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7b1ab-278">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="7b1ab-278">Call client methods from hub</span></span>

<span data-ttu-id="7b1ab-279">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40aspnet/signalr/hubconnection#on) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-279">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="7b1ab-280">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-280">The name of the JavaScript client method.</span></span> <span data-ttu-id="7b1ab-281">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-281">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="7b1ab-282">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-282">Arguments the hub passes to the method.</span></span> <span data-ttu-id="7b1ab-283">Aşağıdaki örnekte, bağımsız değişken değeri `message` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-283">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="7b1ab-284">Yukarıdaki kod, `connection.on` sunucu tarafı kodu yöntemini kullanarak çağırdığında çalıştırılır <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-284">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="7b1ab-285">SignalR ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-285">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="7b1ab-286">En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="7b1ab-286">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="7b1ab-287">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-287">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="7b1ab-288">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="7b1ab-288">Error handling and logging</span></span>

<span data-ttu-id="7b1ab-289">`catch` `start` İstemci tarafı hatalarını işlemek için yönteminin sonuna bir yöntemi zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-289">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="7b1ab-290">`console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-290">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="7b1ab-291">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-291">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="7b1ab-292">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-292">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="7b1ab-293">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-293">Available log levels are as follows:</span></span>

* <span data-ttu-id="7b1ab-294">`signalR.LogLevel.Error`: Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-294">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="7b1ab-295">`Error`Yalnızca iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-295">Logs `Error` messages only.</span></span>
* <span data-ttu-id="7b1ab-296">`signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-296">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="7b1ab-297">Günlükler `Warning` ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-297">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7b1ab-298">`signalR.LogLevel.Information`: Hata olmayan durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-298">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="7b1ab-299">Günlükler `Information` , `Warning` ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-299">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="7b1ab-300">`signalR.LogLevel.Trace`: Trace iletileri.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-300">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="7b1ab-301">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-301">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="7b1ab-302">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-302">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="7b1ab-303">İletiler tarayıcı konsoluna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-303">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="7b1ab-304">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="7b1ab-304">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="7b1ab-305">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="7b1ab-305">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="7b1ab-306">3,0 ' den önce, için JavaScript istemcisi SignalR otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-306">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="7b1ab-307">İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-307">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="7b1ab-308">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7b1ab-308">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="7b1ab-309">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-309">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="7b1ab-310">`start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-310">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="7b1ab-311">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="7b1ab-311">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b1ab-312">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7b1ab-312">Additional resources</span></span>

* [<span data-ttu-id="7b1ab-313">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="7b1ab-313">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="7b1ab-314">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-314">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="7b1ab-315">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-315">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="7b1ab-316">Merkezler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-316">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7b1ab-317">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="7b1ab-317">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="7b1ab-318">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="7b1ab-318">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="7b1ab-319">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="7b1ab-319">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="7b1ab-320">Azure SignalR hizmeti sunucusuz belgeler</span><span class="sxs-lookup"><span data-stu-id="7b1ab-320">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
