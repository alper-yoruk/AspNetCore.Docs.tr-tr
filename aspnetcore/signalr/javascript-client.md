---
title: JavaScript SignalR istemcisi ASP.NET Core
author: bradygaster
description: JavaScript istemcisine ASP.NET Core SignalR genel bakış.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 966e8e171752edb230f6da82203fd901b0fdeaab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768948"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="78232-103">JavaScript SignalR istemcisi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78232-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="78232-104">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="78232-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="78232-105">JavaScript istemci SignalR kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="78232-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="78232-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="78232-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="78232-107">SignalR İstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="78232-107">Install the SignalR client package</span></span>

<span data-ttu-id="78232-108">SignalR JavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="78232-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="78232-109">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="78232-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="78232-110">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="78232-110">Install with npm</span></span>

<span data-ttu-id="78232-111">Visual Studio kullanıyorsanız, kök klasörde **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="78232-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="78232-112">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="78232-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="78232-113">NPM \*node_modules\\ \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="78232-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="78232-114">*Wwwroot\\kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="78232-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="78232-115">*SignalR. js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="78232-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="78232-116">NPM \*node_modules\\ \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="78232-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="78232-117">*Wwwroot\\kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="78232-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="78232-118">*SignalR. js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="78232-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="78232-119">`<script>` Öğesindeki SignalR JavaScript istemcisine başvurun.</span><span class="sxs-lookup"><span data-stu-id="78232-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="78232-120">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="78232-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="78232-121">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="78232-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="78232-122">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="78232-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="78232-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="78232-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="78232-124">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="78232-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="78232-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="78232-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="78232-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="78232-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="78232-127">unpkg</span><span class="sxs-lookup"><span data-stu-id="78232-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="78232-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="78232-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="78232-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="78232-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="78232-130">unpkg</span><span class="sxs-lookup"><span data-stu-id="78232-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="78232-131">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="78232-131">Install with LibMan</span></span>

<span data-ttu-id="78232-132">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="78232-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="78232-133">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="78232-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="78232-134">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="78232-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="78232-135">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="78232-135">Connect to a hub</span></span>

<span data-ttu-id="78232-136">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="78232-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="78232-137">Hub 'ın adı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="78232-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="78232-138">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="78232-138">Cross-origin connections</span></span>

<span data-ttu-id="78232-139">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="78232-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="78232-140">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="78232-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="78232-141">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="78232-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="78232-142">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="78232-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="78232-143">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="78232-143">Call hub methods from client</span></span>

<span data-ttu-id="78232-144">JavaScript istemcileri, [Hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)'un [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="78232-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="78232-145">`invoke` Yöntemi iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="78232-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="78232-146">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="78232-146">The name of the hub method.</span></span> <span data-ttu-id="78232-147">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="78232-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="78232-148">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="78232-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="78232-149">Aşağıdaki örnekte, bağımsız değişken adı `message`.</span><span class="sxs-lookup"><span data-stu-id="78232-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="78232-150">Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="78232-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="78232-151">Azure SignalR hizmeti 'Ni *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz.</span><span class="sxs-lookup"><span data-stu-id="78232-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="78232-152">Daha fazla bilgi için bkz. [ SignalR hizmet belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="78232-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="78232-153">`invoke` Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="78232-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="78232-154">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="78232-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="78232-155">Sunucu üzerindeki yöntem bir hata `Promise` oluşturursa, hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="78232-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="78232-156">Bu durumları `then` ( `catch` veya `await` söz dizimini `Promise` ) işlemek için kendi üzerinde ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="78232-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="78232-157">`send` Yöntemi bir JavaScript `Promise`döndürür.</span><span class="sxs-lookup"><span data-stu-id="78232-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="78232-158">`Promise` İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="78232-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="78232-159">İleti `Promise` gönderilirken bir hata oluşursa, hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="78232-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="78232-160">Bu durumları `then` ( `catch` veya `await` söz dizimini `Promise` ) işlemek için kendi üzerinde ve yöntemlerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="78232-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="78232-161">Kullanmak `send` sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="78232-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="78232-162">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="78232-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="78232-163">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="78232-163">Call client methods from hub</span></span>

<span data-ttu-id="78232-164">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40aspnet/signalr/hubconnection#on) metodunu kullanarak bir yöntemi tanımlayın `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="78232-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="78232-165">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="78232-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="78232-166">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="78232-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="78232-167">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="78232-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="78232-168">Aşağıdaki örnekte, bağımsız değişken değeri `message`.</span><span class="sxs-lookup"><span data-stu-id="78232-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="78232-169">Önceki kod, sunucu `connection.on` tarafı kodu [sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemi kullanılarak çağırdığında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="78232-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="78232-170">ve `SendAsync` `connection.on`' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="78232-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="78232-171">En iyi uygulama olarak, `HubConnection` sonrasında `on` [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="78232-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="78232-172">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="78232-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="78232-173">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="78232-173">Error handling and logging</span></span>

<span data-ttu-id="78232-174">İstemci tarafı `catch` hatalarını işlemek için yönteminin sonuna `start` bir yöntemi zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="78232-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="78232-175">Hataları `console.error` tarayıcı konsoluna çıkarmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="78232-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="78232-176">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="78232-176">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="78232-177">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="78232-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="78232-178">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="78232-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="78232-179">`signalR.LogLevel.Error`&ndash; Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="78232-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="78232-180">Yalnızca `Error` iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="78232-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="78232-181">`signalR.LogLevel.Warning`&ndash; Olası hatalar hakkında uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="78232-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="78232-182">Günlükler `Warning`ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="78232-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="78232-183">`signalR.LogLevel.Information`&ndash; Hatasız durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="78232-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="78232-184">Günlükler `Information`, `Warning`ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="78232-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="78232-185">`signalR.LogLevel.Trace`&ndash; İzleme iletileri.</span><span class="sxs-lookup"><span data-stu-id="78232-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="78232-186">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="78232-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="78232-187">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="78232-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="78232-188">İletiler tarayıcı konsoluna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="78232-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="78232-189">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="78232-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="78232-190">Otomatik olarak yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="78232-190">Automatically reconnect</span></span>

<span data-ttu-id="78232-191">İçin SignalR JavaScript Istemcisi, `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="78232-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="78232-192">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="78232-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="78232-193">Herhangi bir parametre olmadan `withAutomaticReconnect()` , her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="78232-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="78232-194">Yeniden bağlanma girişimlerini başlatmadan önce `HubConnection` , durumuna geçiş `HubConnectionState.Reconnecting` yapmak yerine `onreconnecting` `Disconnected` geri çağırmaları harekete geçer ve otomatik yeniden bağlanma yapılandırması `onclose` `HubConnection` olmadan geri çağırmaları tetikleyerek.</span><span class="sxs-lookup"><span data-stu-id="78232-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="78232-195">Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="78232-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="78232-196">İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="78232-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="78232-197">Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="78232-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="78232-198">Bağlantı sunucuya tamamen yeni göründüğünden `connectionId` `onreconnected` geri aramaya yeni bir verilecek.</span><span class="sxs-lookup"><span data-stu-id="78232-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="78232-199">`onreconnected` Geri aramanın `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur.</span><span class="sxs-lookup"><span data-stu-id="78232-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="78232-200">`withAutomaticReconnect()`, `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="78232-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="78232-201">İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="78232-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="78232-202">Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:</span><span class="sxs-lookup"><span data-stu-id="78232-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="78232-203">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, `withAutomaticReconnect` her bir yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="78232-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="78232-204">Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="78232-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="78232-205">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="78232-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="78232-206">İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.</span><span class="sxs-lookup"><span data-stu-id="78232-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="78232-207">İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.</span><span class="sxs-lookup"><span data-stu-id="78232-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="78232-208">Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="78232-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="78232-209">Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` adlı `IRetryPolicy` `nextRetryDelayInMilliseconds`tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="78232-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="78232-210">`nextRetryDelayInMilliseconds`türünde `RetryContext`tek bir bağımsız değişken alır.</span><span class="sxs-lookup"><span data-stu-id="78232-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="78232-211">, `RetryContext` `previousRetryCount`, `elapsedMilliseconds` Ve `retryReason` `number`sırasıyla bir `number` olan üç özelliğe sahiptir:. `Error`</span><span class="sxs-lookup"><span data-stu-id="78232-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="78232-212">İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve bağlantının kaybolmasına neden olan `retryReason` hata olacaktır.</span><span class="sxs-lookup"><span data-stu-id="78232-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="78232-213">Her başarısız yeniden deneme denemesinden `previousRetryCount` sonra bir `elapsedMilliseconds` artırılır. Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve son yeniden bağlanma girişiminin başarısız olmasına neden olan hata `retryReason` olur.</span><span class="sxs-lookup"><span data-stu-id="78232-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="78232-214">`nextRetryDelayInMilliseconds`sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun `null` yeniden bağlanması `HubConnection` durdurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="78232-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
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

<span data-ttu-id="78232-215">Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="78232-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="78232-216">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="78232-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="78232-217">3,0 ' den önce, için SignalR JavaScript istemcisi otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="78232-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="78232-218">İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="78232-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="78232-219">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="78232-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="78232-220">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="78232-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="78232-221">Bağlantının `onclose` olay `start` işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="78232-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="78232-222">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="78232-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78232-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="78232-223">Additional resources</span></span>

* [<span data-ttu-id="78232-224">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="78232-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="78232-225">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="78232-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="78232-226">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="78232-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="78232-227">Merkezler</span><span class="sxs-lookup"><span data-stu-id="78232-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="78232-228">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="78232-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="78232-229">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="78232-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="78232-230">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="78232-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="78232-231">[Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="78232-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
