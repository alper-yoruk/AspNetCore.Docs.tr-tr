---
title: ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın
author: ardalis
description: ASP.NET Core, Web uygulamalarının Web sunucularından ayrılmasıyla, .NET için açık Web arabirimi 'ni (OWıN) nasıl desteklediğini öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060683"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="4393e-103">ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın</span><span class="sxs-lookup"><span data-stu-id="4393e-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="4393e-104">, [Steve Smith](https://ardalis.com/) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="4393e-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4393e-105">ASP.NET Core, .NET için açık Web arabirimi 'Ni (OWıN) destekler.</span><span class="sxs-lookup"><span data-stu-id="4393e-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="4393e-106">OWIN, Web uygulamalarının Web sunucularından ayrılmasıyla izin verir.</span><span class="sxs-lookup"><span data-stu-id="4393e-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="4393e-107">Bu işlem, ara yazılım için istekleri ve ilişkili yanıtları işlemek üzere bir ardışık düzende kullanılması için standart bir yol tanımlar.</span><span class="sxs-lookup"><span data-stu-id="4393e-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="4393e-108">ASP.NET Core uygulamalar ve ara yazılım, OWıN tabanlı uygulamalar, sunucular ve ara yazılım ile birlikte çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="4393e-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="4393e-109">OWIN, farklı nesne modellerinin birlikte kullanılmasına izin veren bir ayrılmış katman sağlar.</span><span class="sxs-lookup"><span data-stu-id="4393e-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="4393e-110">`Microsoft.AspNetCore.Owin`Paket iki bağdaştırıcı uygulaması sağlar:</span><span class="sxs-lookup"><span data-stu-id="4393e-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="4393e-111">OWıN 'a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4393e-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="4393e-112">ASP.NET Core için OWıN</span><span class="sxs-lookup"><span data-stu-id="4393e-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="4393e-113">Bu, ASP.NET Core bir OWIN uyumlu sunucu/konak üzerinde barındırılmasına veya diğer OWIN uyumlu bileşenlerin ASP.NET Core üzerinde çalıştırılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="4393e-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4393e-114">Bu bağdaştırıcıların kullanılması bir performans maliyetiyle birlikte gelir.</span><span class="sxs-lookup"><span data-stu-id="4393e-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="4393e-115">Yalnızca ASP.NET Core bileşenleri kullanan uygulamalar `Microsoft.AspNetCore.Owin` paketi veya bağdaştırıcıları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="4393e-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="4393e-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4393e-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="4393e-117">ASP.NET Core ardışık düzeninde OWıN ara yazılımı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="4393e-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="4393e-118">ASP.NET Core 'nin OWıN desteği paketin bir parçası olarak dağıtılır `Microsoft.AspNetCore.Owin` .</span><span class="sxs-lookup"><span data-stu-id="4393e-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="4393e-119">Bu paketi yükleyerek, OWıN desteğini projenize aktarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4393e-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="4393e-120">OWıN ara yazılımı, bir arabirim gerektiren [owın belirtimine](https://owin.org/spec/spec/owin-1.0.0.html) `Func<IDictionary<string, object>, Task>` ve belirli anahtarların ayarlanmasını sağlar (örneğin, `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="4393e-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="4393e-121">Aşağıdaki basit OWıN ara yazılımı "Merhaba Dünya" görüntüler:</span><span class="sxs-lookup"><span data-stu-id="4393e-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="4393e-122">Örnek imza bir döndürür `Task` ve `IDictionary<string, object>` Owin için gereken bir şekilde kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4393e-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="4393e-123">Aşağıdaki kod, `OwinHello` genişletme yöntemi ile ASP.NET Core işlem hattına ara yazılım ekleme (yukarıda gösterilen) gösterilmektedir `UseOwin` .</span><span class="sxs-lookup"><span data-stu-id="4393e-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="4393e-124">OWıN ardışık düzeninde gerçekleşecek diğer eylemleri yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4393e-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="4393e-125">Yanıt üst bilgileri yalnızca yanıt akışına ilk yazmaya başlamadan önce değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4393e-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="4393e-126">Performans nedenleriyle birden çok çağrısı `UseOwin` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="4393e-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="4393e-127">OWIN bileşenleri, birlikte gruplandırılmışsa en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="4393e-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="4393e-128">OWIN tabanlı bir sunucuda ASP.NET Core çalıştırın ve WebSockets desteğini kullanın</span><span class="sxs-lookup"><span data-stu-id="4393e-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="4393e-129">ASP.NET Core ile OWıN tabanlı sunucuların özelliklerinin nasıl yararlanılabilir, WebSockets gibi özelliklere erişim için bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="4393e-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="4393e-130">Önceki örnekte kullanılan .NET OWIN Web sunucusu, içinde yerleşik olarak bulunan ve bir ASP.NET Core uygulaması tarafından yararlanılabilir olabilen Web Yuvaları desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="4393e-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="4393e-131">Aşağıdaki örnekte, Web yuvalarını destekleyen ve WebSockets üzerinden sunucuya gönderilen her şeyi yankılayan basit bir Web uygulaması gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4393e-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="4393e-132">OWıN ortamı</span><span class="sxs-lookup"><span data-stu-id="4393e-132">OWIN environment</span></span>

<span data-ttu-id="4393e-133">Kullanarak bir OWıN ortamı oluşturabilirsiniz `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="4393e-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="4393e-134">OWıN tuşları</span><span class="sxs-lookup"><span data-stu-id="4393e-134">OWIN keys</span></span>

<span data-ttu-id="4393e-135">OWIN, `IDictionary<string,object>` BIR http isteği/yanıt değişimi boyunca bilgi iletmek için bir nesneye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="4393e-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="4393e-136">ASP.NET Core aşağıda listelenen anahtarları uygular.</span><span class="sxs-lookup"><span data-stu-id="4393e-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="4393e-137">Bkz. [birincil belirtim, uzantılar](https://owin.org/#spec)ve [Owın anahtar kılavuzları ve ortak anahtarlar](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="4393e-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="4393e-138">İstek verileri (OWıN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4393e-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4393e-139">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-139">Key</span></span>               | <span data-ttu-id="4393e-140">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-140">Value (type)</span></span> | <span data-ttu-id="4393e-141">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-142">owın. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="4393e-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="4393e-143">owın. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="4393e-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="4393e-144">owın. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="4393e-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="4393e-145">owın. Istek yolu</span><span class="sxs-lookup"><span data-stu-id="4393e-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="4393e-146">owın. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="4393e-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="4393e-147">owın. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="4393e-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="4393e-148">owın. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="4393e-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4393e-149">owın. Istek gövdesi</span><span class="sxs-lookup"><span data-stu-id="4393e-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="4393e-150">İstek verileri (OWıN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="4393e-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="4393e-151">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-151">Key</span></span>               | <span data-ttu-id="4393e-152">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-152">Value (type)</span></span> | <span data-ttu-id="4393e-153">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-154">owın. No</span><span class="sxs-lookup"><span data-stu-id="4393e-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="4393e-155">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="4393e-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="4393e-156">Yanıt verileri (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4393e-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4393e-157">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-157">Key</span></span>               | <span data-ttu-id="4393e-158">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-158">Value (type)</span></span> | <span data-ttu-id="4393e-159">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-160">owın. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="4393e-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="4393e-161">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="4393e-161">Optional</span></span> |
| <span data-ttu-id="4393e-162">owın. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="4393e-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="4393e-163">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="4393e-163">Optional</span></span> |
| <span data-ttu-id="4393e-164">owın. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="4393e-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="4393e-165">owın. Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="4393e-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="4393e-166">Diğer veriler (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="4393e-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="4393e-167">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-167">Key</span></span>               | <span data-ttu-id="4393e-168">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-168">Value (type)</span></span> | <span data-ttu-id="4393e-169">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-170">owın. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="4393e-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4393e-171">owın. Sürüm</span><span class="sxs-lookup"><span data-stu-id="4393e-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="4393e-172">Ortak anahtarlar</span><span class="sxs-lookup"><span data-stu-id="4393e-172">Common keys</span></span>

| <span data-ttu-id="4393e-173">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-173">Key</span></span>               | <span data-ttu-id="4393e-174">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-174">Value (type)</span></span> | <span data-ttu-id="4393e-175">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-176">SSL. ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="4393e-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="4393e-177">SSL. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="4393e-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="4393e-178">Server. Remoteıpaddress</span><span class="sxs-lookup"><span data-stu-id="4393e-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4393e-179">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="4393e-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="4393e-180">Server. LocalIPAddress</span><span class="sxs-lookup"><span data-stu-id="4393e-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="4393e-181">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="4393e-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="4393e-182">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="4393e-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="4393e-183">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="4393e-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="4393e-184">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="4393e-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="4393e-185">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-185">Key</span></span>               | <span data-ttu-id="4393e-186">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-186">Value (type)</span></span> | <span data-ttu-id="4393e-187">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-188">SendFile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="4393e-188">sendfile.SendAsync</span></span> | <span data-ttu-id="4393e-189">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="4393e-190">Istek başına</span><span class="sxs-lookup"><span data-stu-id="4393e-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="4393e-191">Donuk v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="4393e-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="4393e-192">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-192">Key</span></span>               | <span data-ttu-id="4393e-193">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-193">Value (type)</span></span> | <span data-ttu-id="4393e-194">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-195">ş. Sürüm</span><span class="sxs-lookup"><span data-stu-id="4393e-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="4393e-196">ş. Yükseltmenizi</span><span class="sxs-lookup"><span data-stu-id="4393e-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="4393e-197">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4393e-198">ş. Ka</span><span class="sxs-lookup"><span data-stu-id="4393e-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="4393e-199">ş. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="4393e-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="4393e-200">WebSocket v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="4393e-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="4393e-201">Anahtar</span><span class="sxs-lookup"><span data-stu-id="4393e-201">Key</span></span>               | <span data-ttu-id="4393e-202">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="4393e-202">Value (type)</span></span> | <span data-ttu-id="4393e-203">Açıklama</span><span class="sxs-lookup"><span data-stu-id="4393e-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="4393e-204">WebSocket. Sürüm</span><span class="sxs-lookup"><span data-stu-id="4393e-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="4393e-205">WebSocket. Ettiğinizde</span><span class="sxs-lookup"><span data-stu-id="4393e-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="4393e-206">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="4393e-207">WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="4393e-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="4393e-208">Spec dışı</span><span class="sxs-lookup"><span data-stu-id="4393e-208">Non-spec</span></span> |
| <span data-ttu-id="4393e-209">WebSocket. Alt protokolü</span><span class="sxs-lookup"><span data-stu-id="4393e-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="4393e-210">Bkz. [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5,5</span><span class="sxs-lookup"><span data-stu-id="4393e-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="4393e-211">WebSocket. SendAsync</span><span class="sxs-lookup"><span data-stu-id="4393e-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="4393e-212">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4393e-213">WebSocket. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="4393e-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="4393e-214">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4393e-215">WebSocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="4393e-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="4393e-216">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="4393e-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="4393e-217">WebSocket. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="4393e-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="4393e-218">WebSocket. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="4393e-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="4393e-219">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="4393e-219">Optional</span></span> |
| <span data-ttu-id="4393e-220">WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="4393e-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="4393e-221">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="4393e-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="4393e-222">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4393e-222">Additional resources</span></span>

* [<span data-ttu-id="4393e-223">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="4393e-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="4393e-224">Sunucular</span><span class="sxs-lookup"><span data-stu-id="4393e-224">Servers</span></span>](xref:fundamentals/servers/index)
