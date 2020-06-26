---
title: ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın
author: ardalis
description: ASP.NET Core, Web uygulamalarının Web sunucularından ayrılmasıyla, .NET için açık Web arabirimi 'ni (OWıN) nasıl desteklediğini öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: 3d90788fa300bf099f44aa284e77879698240a74
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407830"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="d0f0c-103">ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın</span><span class="sxs-lookup"><span data-stu-id="d0f0c-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="d0f0c-104">, [Steve Smith](https://ardalis.com/) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="d0f0c-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d0f0c-105">ASP.NET Core, .NET için açık Web arabirimi 'Ni (OWıN) destekler.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="d0f0c-106">OWIN, Web uygulamalarının Web sunucularından ayrılmasıyla izin verir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="d0f0c-107">Bu işlem, ara yazılım için istekleri ve ilişkili yanıtları işlemek üzere bir ardışık düzende kullanılması için standart bir yol tanımlar.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="d0f0c-108">ASP.NET Core uygulamalar ve ara yazılım, OWıN tabanlı uygulamalar, sunucular ve ara yazılım ile birlikte çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="d0f0c-109">OWIN, farklı nesne modellerinin birlikte kullanılmasına izin veren bir ayrılmış katman sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="d0f0c-110">`Microsoft.AspNetCore.Owin`Paket iki bağdaştırıcı uygulaması sağlar:</span><span class="sxs-lookup"><span data-stu-id="d0f0c-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="d0f0c-111">OWıN 'a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0f0c-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="d0f0c-112">ASP.NET Core için OWıN</span><span class="sxs-lookup"><span data-stu-id="d0f0c-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="d0f0c-113">Bu, ASP.NET Core bir OWIN uyumlu sunucu/konak üzerinde barındırılmasına veya diğer OWIN uyumlu bileşenlerin ASP.NET Core üzerinde çalıştırılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f0c-114">Bu bağdaştırıcıların kullanılması bir performans maliyetiyle birlikte gelir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="d0f0c-115">Yalnızca ASP.NET Core bileşenleri kullanan uygulamalar `Microsoft.AspNetCore.Owin` paketi veya bağdaştırıcıları kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="d0f0c-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0f0c-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="d0f0c-117">ASP.NET Core ardışık düzeninde OWıN ara yazılımı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="d0f0c-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="d0f0c-118">ASP.NET Core 'nin OWıN desteği paketin bir parçası olarak dağıtılır `Microsoft.AspNetCore.Owin` .</span><span class="sxs-lookup"><span data-stu-id="d0f0c-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="d0f0c-119">Bu paketi yükleyerek, OWıN desteğini projenize aktarabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="d0f0c-120">OWıN ara yazılımı, bir arabirim gerektiren [owın belirtimine](https://owin.org/spec/spec/owin-1.0.0.html) `Func<IDictionary<string, object>, Task>` ve belirli anahtarların ayarlanmasını sağlar (örneğin, `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="d0f0c-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="d0f0c-121">Aşağıdaki basit OWıN ara yazılımı "Merhaba Dünya" görüntüler:</span><span class="sxs-lookup"><span data-stu-id="d0f0c-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="d0f0c-122">Örnek imza bir döndürür `Task` ve `IDictionary<string, object>` Owin için gereken bir şekilde kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="d0f0c-123">Aşağıdaki kod, `OwinHello` genişletme yöntemi ile ASP.NET Core işlem hattına ara yazılım ekleme (yukarıda gösterilen) gösterilmektedir `UseOwin` .</span><span class="sxs-lookup"><span data-stu-id="d0f0c-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="d0f0c-124">OWıN ardışık düzeninde gerçekleşecek diğer eylemleri yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f0c-125">Yanıt üst bilgileri yalnızca yanıt akışına ilk yazmaya başlamadan önce değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="d0f0c-126">Performans nedenleriyle birden çok çağrısı `UseOwin` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="d0f0c-127">OWIN bileşenleri, birlikte gruplandırılmışsa en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="d0f0c-128">OWIN tabanlı bir sunucuda barındırma ASP.NET Core kullanma</span><span class="sxs-lookup"><span data-stu-id="d0f0c-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="d0f0c-129">OWIN tabanlı sunucular ASP.NET Core uygulamaları barındırabilirler.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="d0f0c-130">Bu tür bir sunucu, .NET OWıN Web sunucusu olan [Nowin](https://github.com/Bobris/Nowin)'dir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="d0f0c-131">Bu makalenin örneğinde, Nowin 'a başvuran bir proje ekledik ve bunu `IServer` kendi kendine barındırma ASP.NET Core özellikli bir özelliği oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="d0f0c-132">`IServer`, bir `Features` özellik ve yöntem gerektiren bir arabirimdir `Start` .</span><span class="sxs-lookup"><span data-stu-id="d0f0c-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="d0f0c-133">`Start`, sunucuyu yapılandırmadan ve başlatmaktan sorumludur; bu durumda, ıveraddressesözelliğinden ayrıştırılmış adresleri belirleyen bir dizi Fluent API çağrısı aracılığıyla yapılır.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="d0f0c-134">Değişkenin akıcı yapılandırmasının, `_builder` isteklerin `appFunc` metotta daha önce tanımlanan tarafından işleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="d0f0c-135">Bu `Func` , gelen istekleri işlemek için her istekte çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="d0f0c-136">`IWebHostBuilder`Nowin sunucusunu eklemeyi ve yapılandırmayı kolaylaştırmak için de bir uzantı ekleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="d0f0c-137">Bu özel sunucuyu kullanarak bir ASP.NET Core uygulaması çalıştırmak için bu uygulamada *program.cs* içindeki uzantıyı çağırın:</span><span class="sxs-lookup"><span data-stu-id="d0f0c-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="d0f0c-138">[ASP.NET Core sunucuları](xref:fundamentals/servers/index)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="d0f0c-139">OWIN tabanlı bir sunucuda ASP.NET Core çalıştırın ve WebSockets desteğini kullanın</span><span class="sxs-lookup"><span data-stu-id="d0f0c-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="d0f0c-140">ASP.NET Core ile OWıN tabanlı sunucuların özelliklerinin nasıl yararlanılabilir, WebSockets gibi özelliklere erişim için bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="d0f0c-141">Önceki örnekte kullanılan .NET OWIN Web sunucusu, içinde yerleşik olarak bulunan ve bir ASP.NET Core uygulaması tarafından yararlanılabilir olabilen Web Yuvaları desteği içerir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="d0f0c-142">Aşağıdaki örnekte, Web yuvalarını destekleyen ve WebSockets üzerinden sunucuya gönderilen her şeyi yankılayan basit bir Web uygulaması gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="d0f0c-143">Bu [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) , öncekiyle aynı kullanılarak yapılandırılmış ve `NowinServer` tek fark, uygulamanın yönteminde nasıl yapılandırıldığı ile aynıdır `Configure` .</span><span class="sxs-lookup"><span data-stu-id="d0f0c-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="d0f0c-144">[Basit bir WebSocket istemcisi](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) kullanan bir test, uygulamayı gösterir:</span><span class="sxs-lookup"><span data-stu-id="d0f0c-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Web yuvası test Istemcisi](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="d0f0c-146">OWıN ortamı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-146">OWIN environment</span></span>

<span data-ttu-id="d0f0c-147">Kullanarak bir OWıN ortamı oluşturabilirsiniz `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="d0f0c-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="d0f0c-148">OWıN tuşları</span><span class="sxs-lookup"><span data-stu-id="d0f0c-148">OWIN keys</span></span>

<span data-ttu-id="d0f0c-149">OWIN, `IDictionary<string,object>` BIR http isteği/yanıt değişimi boyunca bilgi iletmek için bir nesneye bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="d0f0c-150">ASP.NET Core aşağıda listelenen anahtarları uygular.</span><span class="sxs-lookup"><span data-stu-id="d0f0c-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="d0f0c-151">Bkz. [birincil belirtim, uzantılar](https://owin.org/#spec)ve [Owın anahtar kılavuzları ve ortak anahtarlar](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="d0f0c-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="d0f0c-152">İstek verileri (OWıN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0f0c-153">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-153">Key</span></span>               | <span data-ttu-id="d0f0c-154">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-154">Value (type)</span></span> | <span data-ttu-id="d0f0c-155">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-156">owın. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="d0f0c-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="d0f0c-157">owın. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="d0f0c-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-158">owın. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="d0f0c-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-159">owın. Istek yolu</span><span class="sxs-lookup"><span data-stu-id="d0f0c-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="d0f0c-160">owın. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="d0f0c-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-161">owın. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="d0f0c-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-162">owın. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="d0f0c-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d0f0c-163">owın. Istek gövdesi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="d0f0c-164">İstek verileri (OWıN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="d0f0c-165">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-165">Key</span></span>               | <span data-ttu-id="d0f0c-166">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-166">Value (type)</span></span> | <span data-ttu-id="d0f0c-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-168">owın. No</span><span class="sxs-lookup"><span data-stu-id="d0f0c-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="d0f0c-169">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="d0f0c-170">Yanıt verileri (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0f0c-171">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-171">Key</span></span>               | <span data-ttu-id="d0f0c-172">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-172">Value (type)</span></span> | <span data-ttu-id="d0f0c-173">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-174">owın. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="d0f0c-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="d0f0c-175">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-175">Optional</span></span> |
| <span data-ttu-id="d0f0c-176">owın. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="d0f0c-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="d0f0c-177">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-177">Optional</span></span> |
| <span data-ttu-id="d0f0c-178">owın. ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="d0f0c-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d0f0c-179">owın. Yanıt gövdesi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="d0f0c-180">Diğer veriler (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0f0c-181">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-181">Key</span></span>               | <span data-ttu-id="d0f0c-182">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-182">Value (type)</span></span> | <span data-ttu-id="d0f0c-183">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-184">owın. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d0f0c-185">owın. Sürüm</span><span class="sxs-lookup"><span data-stu-id="d0f0c-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="d0f0c-186">Ortak anahtarlar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-186">Common keys</span></span>

| <span data-ttu-id="d0f0c-187">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-187">Key</span></span>               | <span data-ttu-id="d0f0c-188">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-188">Value (type)</span></span> | <span data-ttu-id="d0f0c-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-190">SSL. ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="d0f0c-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="d0f0c-191">SSL. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="d0f0c-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="d0f0c-192">Server. Remoteıpaddress</span><span class="sxs-lookup"><span data-stu-id="d0f0c-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-193">Server. RemotePort</span><span class="sxs-lookup"><span data-stu-id="d0f0c-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="d0f0c-194">Server. LocalIPAddress</span><span class="sxs-lookup"><span data-stu-id="d0f0c-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-195">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="d0f0c-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="d0f0c-196">Server. IsLocal</span><span class="sxs-lookup"><span data-stu-id="d0f0c-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="d0f0c-197">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="d0f0c-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="d0f0c-198">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0f0c-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="d0f0c-199">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-199">Key</span></span>               | <span data-ttu-id="d0f0c-200">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-200">Value (type)</span></span> | <span data-ttu-id="d0f0c-201">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-202">SendFile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="d0f0c-202">sendfile.SendAsync</span></span> | <span data-ttu-id="d0f0c-203">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="d0f0c-204">Istek başına</span><span class="sxs-lookup"><span data-stu-id="d0f0c-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="d0f0c-205">Donuk v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0f0c-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="d0f0c-206">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-206">Key</span></span>               | <span data-ttu-id="d0f0c-207">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-207">Value (type)</span></span> | <span data-ttu-id="d0f0c-208">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-209">ş. Sürüm</span><span class="sxs-lookup"><span data-stu-id="d0f0c-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="d0f0c-210">ş. Yükseltmenizi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="d0f0c-211">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d0f0c-212">ş. Ka</span><span class="sxs-lookup"><span data-stu-id="d0f0c-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="d0f0c-213">ş. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="d0f0c-214">WebSocket v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0f0c-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="d0f0c-215">Anahtar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-215">Key</span></span>               | <span data-ttu-id="d0f0c-216">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-216">Value (type)</span></span> | <span data-ttu-id="d0f0c-217">Açıklama</span><span class="sxs-lookup"><span data-stu-id="d0f0c-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0f0c-218">WebSocket. Sürüm</span><span class="sxs-lookup"><span data-stu-id="d0f0c-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="d0f0c-219">WebSocket. Ettiğinizde</span><span class="sxs-lookup"><span data-stu-id="d0f0c-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="d0f0c-220">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d0f0c-221">WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="d0f0c-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="d0f0c-222">Spec dışı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-222">Non-spec</span></span> |
| <span data-ttu-id="d0f0c-223">WebSocket. Alt protokolü</span><span class="sxs-lookup"><span data-stu-id="d0f0c-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="d0f0c-224">Bkz. [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5,5</span><span class="sxs-lookup"><span data-stu-id="d0f0c-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="d0f0c-225">WebSocket. SendAsync</span><span class="sxs-lookup"><span data-stu-id="d0f0c-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="d0f0c-226">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0f0c-227">WebSocket. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="d0f0c-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="d0f0c-228">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0f0c-229">WebSocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="d0f0c-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="d0f0c-230">Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0f0c-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0f0c-231">WebSocket. Calliptal edildi</span><span class="sxs-lookup"><span data-stu-id="d0f0c-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d0f0c-232">WebSocket. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="d0f0c-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="d0f0c-233">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-233">Optional</span></span> |
| <span data-ttu-id="d0f0c-234">WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="d0f0c-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="d0f0c-235">İsteğe Bağlı</span><span class="sxs-lookup"><span data-stu-id="d0f0c-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d0f0c-236">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d0f0c-236">Additional resources</span></span>

* [<span data-ttu-id="d0f0c-237">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="d0f0c-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d0f0c-238">Sunucular</span><span class="sxs-lookup"><span data-stu-id="d0f0c-238">Servers</span></span>](xref:fundamentals/servers/index)
