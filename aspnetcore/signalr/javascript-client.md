---
<span data-ttu-id="c5de2-101">Başlık: ' ASP.NET Core SignalR JavaScript istemcisi ' Yazar: Açıklama: ' ASP.NET Core SignalR JavaScript Istemcisine genel bakış. '</span><span class="sxs-lookup"><span data-stu-id="c5de2-101">title: 'ASP.NET Core SignalR JavaScript client' author: description: 'Overview of ASP.NET Core SignalR JavaScript client.'</span></span>
<span data-ttu-id="c5de2-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="c5de2-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c5de2-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c5de2-103">'Blazor'</span></span>
- <span data-ttu-id="c5de2-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c5de2-104">'Identity'</span></span>
- <span data-ttu-id="c5de2-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c5de2-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c5de2-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c5de2-106">'Razor'</span></span>
- <span data-ttu-id="c5de2-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="c5de2-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="c5de2-108">SignalRJavaScript istemcisi ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c5de2-108">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="c5de2-109">, [Oychel Appel](https://twitter.com/rachelappel) tarafından</span><span class="sxs-lookup"><span data-stu-id="c5de2-109">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="c5de2-110">SignalRJavaScript istemci kitaplığı ASP.NET Core, geliştiricilerin sunucu tarafı hub kodunu çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-110">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="c5de2-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c5de2-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="c5de2-112">SignalRİstemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="c5de2-112">Install the SignalR client package</span></span>

<span data-ttu-id="c5de2-113">SignalRJavaScript istemci kitaplığı [NPM](https://www.npmjs.com/) paketi olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-113">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="c5de2-114">Aşağıdaki bölümlerde, istemci kitaplığını yüklemenin farklı yolları ana hatlarıyla verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-114">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="c5de2-115">NPM ile yüklensin</span><span class="sxs-lookup"><span data-stu-id="c5de2-115">Install with npm</span></span>

<span data-ttu-id="c5de2-116">Visual Studio kullanıyorsanız, kök klasörde **Paket Yöneticisi konsolundan** aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-116">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="c5de2-117">Visual Studio Code için, **Tümleşik terminalden**aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-117">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="c5de2-118">NPM \*node_modules \\ @microsoft\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="c5de2-118">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c5de2-119">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c5de2-119">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c5de2-120">*SignalR. js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-120">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="c5de2-121">NPM \*node_modules \\ @aspnet\signalr\dist\browser \* klasöre paket içeriğini yüklüyor.</span><span class="sxs-lookup"><span data-stu-id="c5de2-121">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="c5de2-122">*Wwwroot \\ kitaplığı* klasörünün altında *SignalR* adlı yeni bir klasör oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c5de2-122">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="c5de2-123">*SignalR. js* dosyasını *wwwroot\lib\signalr* klasörüne kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-123">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="c5de2-124">SignalRÖğesindeki JavaScript istemcisine başvurun `<script>` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-124">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="c5de2-125">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c5de2-125">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="c5de2-126">Content Delivery Network kullanma (CDN)</span><span class="sxs-lookup"><span data-stu-id="c5de2-126">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="c5de2-127">İstemci kitaplığını NPM önkoşulu olmadan kullanmak için, istemci kitaplığının CDN ile barındırılan bir kopyasına başvurun.</span><span class="sxs-lookup"><span data-stu-id="c5de2-127">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="c5de2-128">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="c5de2-128">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="c5de2-129">İstemci kitaplığı aşağıdaki CDNs üzerinde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c5de2-129">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="c5de2-130">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c5de2-130">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="c5de2-131">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c5de2-131">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="c5de2-132">unpkg</span><span class="sxs-lookup"><span data-stu-id="c5de2-132">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="c5de2-133">cdnjs</span><span class="sxs-lookup"><span data-stu-id="c5de2-133">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="c5de2-134">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="c5de2-134">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="c5de2-135">unpkg</span><span class="sxs-lookup"><span data-stu-id="c5de2-135">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="c5de2-136">LibMan ile Install</span><span class="sxs-lookup"><span data-stu-id="c5de2-136">Install with LibMan</span></span>

<span data-ttu-id="c5de2-137">[Libman](xref:client-side/libman/index) , CDN ile barındırılan istemci kitaplığından belirli istemci kitaplığı dosyalarını yüklemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-137">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="c5de2-138">Örneğin, yalnızca küçültülmüş JavaScript dosyasını projeye ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c5de2-138">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="c5de2-139">Bu yaklaşım hakkında daha fazla bilgi için bkz. [ SignalR Istemci kitaplığı ekleme](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="c5de2-139">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="c5de2-140">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="c5de2-140">Connect to a hub</span></span>

<span data-ttu-id="c5de2-141">Aşağıdaki kod bir bağlantı oluşturur ve başlatır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-141">The following code creates and starts a connection.</span></span> <span data-ttu-id="c5de2-142">Hub 'ın adı büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-142">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="c5de2-143">Çapraz kaynak bağlantıları</span><span class="sxs-lookup"><span data-stu-id="c5de2-143">Cross-origin connections</span></span>

<span data-ttu-id="c5de2-144">Genellikle, tarayıcılar istenen sayfayla aynı etki alanındaki bağlantıları yükler.</span><span class="sxs-lookup"><span data-stu-id="c5de2-144">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="c5de2-145">Ancak, başka bir etki alanına bağlantı gerektiğinde bu durumlar vardır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-145">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="c5de2-146">Kötü amaçlı bir sitenin başka bir siteden hassas verileri okumasını engellemek için, [Çıkış](xref:security/cors) noktaları varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-146">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="c5de2-147">Bir çapraz kaynak isteğine izin vermek için `Startup` sınıfında etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="c5de2-147">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="c5de2-148">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="c5de2-148">Call hub methods from client</span></span>

<span data-ttu-id="c5de2-149">JavaScript istemcileri, [Hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)'un [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) yöntemi aracılığıyla hub 'larda ortak yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-149">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="c5de2-150">`invoke`Yöntemi iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="c5de2-150">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="c5de2-151">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="c5de2-151">The name of the hub method.</span></span> <span data-ttu-id="c5de2-152">Aşağıdaki örnekte, hub 'daki Yöntem adı `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-152">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="c5de2-153">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="c5de2-153">Any arguments defined in the hub method.</span></span> <span data-ttu-id="c5de2-154">Aşağıdaki örnekte, bağımsız değişken adı `message` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-154">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="c5de2-155">Örnek kod, Internet Explorer hariç tüm büyük tarayıcıların güncel sürümlerinde desteklenen ok işlev sözdizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-155">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="c5de2-156">Azure SignalR hizmeti 'Ni *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz.</span><span class="sxs-lookup"><span data-stu-id="c5de2-156">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="c5de2-157">Daha fazla bilgi için bkz. [ SignalR hizmet belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="c5de2-157">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="c5de2-158">`invoke`Yöntemi bir JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)döndürür.</span><span class="sxs-lookup"><span data-stu-id="c5de2-158">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="c5de2-159">, `Promise` Sunucu üzerindeki yöntemi döndürdüğünde (varsa) dönüş değeri ile çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-159">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="c5de2-160">Sunucu üzerindeki yöntem bir hata oluşturursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-160">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c5de2-161">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-161">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="c5de2-162">`send`Yöntemi bir JavaScript döndürür `Promise` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-162">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="c5de2-163">`Promise`İleti sunucuya gönderildiğinde çözümlenir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-163">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="c5de2-164">İleti gönderilirken bir hata oluşursa, `Promise` hata iletisiyle reddedilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-164">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="c5de2-165">`then` `catch` `Promise` Bu durumları (veya söz dizimini) işlemek için kendi üzerinde ve yöntemlerini kullanın `await` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-165">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="c5de2-166">Kullanmak `send` Sunucu iletiyi almaa kadar beklemez.</span><span class="sxs-lookup"><span data-stu-id="c5de2-166">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="c5de2-167">Sonuç olarak, sunucudan veri veya hata döndürülmesi mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-167">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="c5de2-168">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="c5de2-168">Call client methods from hub</span></span>

<span data-ttu-id="c5de2-169">Hub 'dan ileti almak için, öğesinin [on](/javascript/api/%40aspnet/signalr/hubconnection#on) metodunu kullanarak bir yöntemi tanımlayın `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-169">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="c5de2-170">JavaScript istemci yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="c5de2-170">The name of the JavaScript client method.</span></span> <span data-ttu-id="c5de2-171">Aşağıdaki örnekte, yöntem adı ' dir `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-171">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="c5de2-172">Hub 'ın yönteme geçirdiği bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="c5de2-172">Arguments the hub passes to the method.</span></span> <span data-ttu-id="c5de2-173">Aşağıdaki örnekte, bağımsız değişken değeri `message` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-173">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="c5de2-174">Önceki kod, `connection.on` sunucu tarafı kodu [Sendadsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) yöntemi kullanılarak çağırdığında çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-174">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="c5de2-175">ve ' de tanımlanan bağımsız değişkenlerle, hangi istemci yönteminin çağrılacağını belirler `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-175"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="c5de2-176">En iyi uygulama olarak, sonrasında [Başlangıç](/javascript/api/%40aspnet/signalr/hubconnection#start) yöntemini çağırın `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-176">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="c5de2-177">Bunun yapılması, işleyicilerinin herhangi bir ileti alınmadan önce kaydolmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-177">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="c5de2-178">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="c5de2-178">Error handling and logging</span></span>

<span data-ttu-id="c5de2-179">`catch` `start` İstemci tarafı hatalarını işlemek için yönteminin sonuna bir yöntemi zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="c5de2-179">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="c5de2-180">`console.error`Hataları tarayıcı konsoluna çıkarmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-180">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="c5de2-181">Bağlantı yapıldığında günlüğe bir günlükçü ve olay türü geçirerek istemci tarafı günlük izlemeyi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-181">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="c5de2-182">İletiler, belirtilen günlük düzeyi ve daha yükseği ile günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-182">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="c5de2-183">Kullanılabilir günlük düzeyleri aşağıdaki gibidir:</span><span class="sxs-lookup"><span data-stu-id="c5de2-183">Available log levels are as follows:</span></span>

* <span data-ttu-id="c5de2-184">`signalR.LogLevel.Error`: Hata iletileri.</span><span class="sxs-lookup"><span data-stu-id="c5de2-184">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="c5de2-185">`Error`Yalnızca iletileri günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c5de2-185">Logs `Error` messages only.</span></span>
* <span data-ttu-id="c5de2-186">`signalR.LogLevel.Warning`: Olası hatalarla ilgili uyarı iletileri.</span><span class="sxs-lookup"><span data-stu-id="c5de2-186">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="c5de2-187">Günlükler `Warning` ve `Error` mesajlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-187">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c5de2-188">`signalR.LogLevel.Information`: Hata olmayan durum iletileri.</span><span class="sxs-lookup"><span data-stu-id="c5de2-188">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="c5de2-189">Günlükler `Information` , `Warning` ve `Error` iletileri.</span><span class="sxs-lookup"><span data-stu-id="c5de2-189">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="c5de2-190">`signalR.LogLevel.Trace`: Trace iletileri.</span><span class="sxs-lookup"><span data-stu-id="c5de2-190">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="c5de2-191">Hub ve istemci arasında taşınan veriler de dahil olmak üzere her şeyi günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c5de2-191">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="c5de2-192">Günlük düzeyini yapılandırmak için [Hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) üzerinde [configurelogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-192">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="c5de2-193">İletiler tarayıcı konsoluna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-193">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="c5de2-194">İstemcileri yeniden bağla</span><span class="sxs-lookup"><span data-stu-id="c5de2-194">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="c5de2-195">Otomatik olarak yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="c5de2-195">Automatically reconnect</span></span>

<span data-ttu-id="c5de2-196">İçin JavaScript istemcisi, SignalR `withAutomaticReconnect` [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)'daki yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-196">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="c5de2-197">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="c5de2-197">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="c5de2-198">Herhangi bir parametre olmadan, `withAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-198">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="c5de2-199">Yeniden bağlanma girişimlerini başlatmadan önce, durumuna `HubConnection` geçiş yapmak `HubConnectionState.Reconnecting` yerine geri çağırmaları harekete geçer ve `onreconnecting` `Disconnected` `onclose` `HubConnection` Otomatik yeniden bağlanma yapılandırması olmadan geri çağırmaları tetikleyerek.</span><span class="sxs-lookup"><span data-stu-id="c5de2-199">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="c5de2-200">Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-200">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c5de2-201">İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` durum durumuna geçer ve `onreconnected` geri çağırmaları harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-201">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="c5de2-202">Bu, kullanıcılara bağlantı yeniden kurulmadığını bildirmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-202">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="c5de2-203">Bağlantı sunucuya tamamen yeni göründüğünden geri aramaya yeni bir verilecek `connectionId` `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-203">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="c5de2-204">`onreconnected`Geri aramanın `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa tanımsız olur.</span><span class="sxs-lookup"><span data-stu-id="c5de2-204">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c5de2-205">`withAutomaticReconnect()`, `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="c5de2-205">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="c5de2-206">İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) geri aramalarını harekete geçirebilir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-206">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="c5de2-207">Bu, kullanıcılara bağlantının kalıcı olarak kaybedilmediğini bildirme ve sayfayı yenilemeyi önerme olanağı sağlar:</span><span class="sxs-lookup"><span data-stu-id="c5de2-207">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="c5de2-208">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `withAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="c5de2-208">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="c5de2-209">Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-209">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="c5de2-210">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-210">This is also true for the default configuration.</span></span>

<span data-ttu-id="c5de2-211">İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-211">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c5de2-212">İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.</span><span class="sxs-lookup"><span data-stu-id="c5de2-212">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="c5de2-213">Özel davranış daha sonra varsayılan şekilde yeniden bağlantı kurmayı denemek yerine, üçüncü yeniden bağlantı girişimi başarısızlığından sonra varsayılan davranıştan sonra yeniden bir kez daha fazla yeniden bağlantı kurmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-213">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="c5de2-214">Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `withAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-214">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="c5de2-215">`nextRetryDelayInMilliseconds`türünde tek bir bağımsız değişken alır `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="c5de2-215">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="c5de2-216">,, `RetryContext` `previousRetryCount` `elapsedMilliseconds` Ve `retryReason` sırasıyla bir olan `number` `number` üç özelliğe `Error` sahiptir:.</span><span class="sxs-lookup"><span data-stu-id="c5de2-216">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="c5de2-217">İlk yeniden bağlanma denemesinden önce, `previousRetryCount` ve `elapsedMilliseconds` sıfır olur ve `retryReason` bağlantının kaybolmasına neden olan hata olacaktır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-217">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="c5de2-218">Her başarısız yeniden deneme denemesinden sonra `previousRetryCount` bir artırılır `elapsedMilliseconds` . Bu, şimdiye kadar geçen sürenin süresini yansıtacak şekilde güncelleştirilir ve `retryReason` son yeniden bağlanma girişiminin başarısız olmasına neden olan hata olur.</span><span class="sxs-lookup"><span data-stu-id="c5de2-218">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="c5de2-219">`nextRetryDelayInMilliseconds`sonraki yeniden bağlanma girişiminden önce beklenecek milisaniye sayısını temsil eden bir sayı döndürmelidir veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c5de2-219">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="c5de2-220">Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="c5de2-220">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="c5de2-221">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="c5de2-221">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="c5de2-222">3,0 ' den önce, için JavaScript istemcisi SignalR otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="c5de2-222">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="c5de2-223">İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="c5de2-223">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="c5de2-224">Aşağıdaki kod, genel bir el ile yeniden bağlantı yaklaşımını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c5de2-224">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="c5de2-225">Bağlantıyı başlatmak için bir işlev (Bu durumda `start` işlev) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c5de2-225">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="c5de2-226">`start`Bağlantının `onclose` olay işleyicisindeki işlevi çağırın.</span><span class="sxs-lookup"><span data-stu-id="c5de2-226">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="c5de2-227">Gerçek dünyada bir uygulama, bir üstel geri kapatmayı kullanır veya vermeden önce belirtilen sayıda kez yeniden dener.</span><span class="sxs-lookup"><span data-stu-id="c5de2-227">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c5de2-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c5de2-228">Additional resources</span></span>

* [<span data-ttu-id="c5de2-229">JavaScript API'si başvurusu</span><span class="sxs-lookup"><span data-stu-id="c5de2-229">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="c5de2-230">JavaScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="c5de2-230">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c5de2-231">WebPack ve TypeScript öğreticisi</span><span class="sxs-lookup"><span data-stu-id="c5de2-231">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="c5de2-232">Merkezler</span><span class="sxs-lookup"><span data-stu-id="c5de2-232">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c5de2-233">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="c5de2-233">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c5de2-234">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="c5de2-234">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="c5de2-235">Çıkış noktaları arası Istekler (CORS)</span><span class="sxs-lookup"><span data-stu-id="c5de2-235">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="c5de2-236">[Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="c5de2-236">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
