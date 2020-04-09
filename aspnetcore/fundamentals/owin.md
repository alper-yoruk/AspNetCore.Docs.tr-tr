---
title: ASP.NET Core ile .NET (OWIN) için Açık Web Arabirimi
author: ardalis
description: ASP.NET Core'un, web uygulamalarının web sunucularından ayrılmasına olanak tanıyan .NET (OWIN) için Açık Web Arabirimini nasıl desteklediğini keşfedin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666686"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="941fc-103">ASP.NET Core ile .NET (OWIN) için Açık Web Arabirimi</span><span class="sxs-lookup"><span data-stu-id="941fc-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="941fc-104">Yazar: [Steve Smith](https://ardalis.com/) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="941fc-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="941fc-105">ASP.NET Core,.NET (OWIN) için Açık Web Arabirimini destekler.</span><span class="sxs-lookup"><span data-stu-id="941fc-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="941fc-106">OWIN, web uygulamalarının web sunucularından ayrılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="941fc-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="941fc-107">Ara yazılımın istekleri ve ilişkili yanıtları işlemek için bir ardışık alanda kullanılması için standart bir yol tanımlar.</span><span class="sxs-lookup"><span data-stu-id="941fc-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="941fc-108">ASP.NET Core uygulamaları ve ara yazılımlar OWIN tabanlı uygulamalar, sunucular ve ara yazılımlarla birlikte çalışabilir.</span><span class="sxs-lookup"><span data-stu-id="941fc-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="941fc-109">OWIN, birbirinden farklı nesne modellerine sahip iki çerçevenin birlikte kullanılmasını sağlayan bir ayrıştırma katmanı sağlar.</span><span class="sxs-lookup"><span data-stu-id="941fc-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="941fc-110">Paket `Microsoft.AspNetCore.Owin` iki bağdaştırıcı uygulaması sağlar:</span><span class="sxs-lookup"><span data-stu-id="941fc-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="941fc-111">ASP.NET Core OWIN için</span><span class="sxs-lookup"><span data-stu-id="941fc-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="941fc-112">OWIN ASP.NET Çekirdek için</span><span class="sxs-lookup"><span data-stu-id="941fc-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="941fc-113">Bu, ASP.NET Core'un OWIN uyumlu bir sunucunun/ana bilgisayarın üzerinde barındırılmasına veya diğer OWIN uyumlu bileşenlerin ASP.NET Core'un üzerinde çalıştırılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="941fc-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="941fc-114">Bu bağdaştırıcıları kullanmak bir performans maliyetiyle birlikte gelir.</span><span class="sxs-lookup"><span data-stu-id="941fc-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="941fc-115">Yalnızca ASP.NET Core bileşenlerini kullanan `Microsoft.AspNetCore.Owin` uygulamalar paketi veya bağdaştırıcıları kullanmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="941fc-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="941fc-116">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="941fc-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="941fc-117">ASP.NET Core boru hattında OWIN ara yazılımının çalıştırıl</span><span class="sxs-lookup"><span data-stu-id="941fc-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="941fc-118">ASP.NET Core'un OWIN desteği `Microsoft.AspNetCore.Owin` paketin bir parçası olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="941fc-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="941fc-119">Bu paketi yükleyerek Projenize OWIN desteği alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="941fc-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="941fc-120">OWIN ara yazılım, arabirim `Func<IDictionary<string, object>, Task>` gerektiren [OWIN belirtimine](https://owin.org/spec/spec/owin-1.0.0.html)uygundur ve belirli `owin.ResponseBody`tuşlar (örneğin) ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="941fc-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="941fc-121">Aşağıdaki basit OWIN middleware görüntüler "Hello World":</span><span class="sxs-lookup"><span data-stu-id="941fc-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="941fc-122">Örnek imza a `Task` döndürür `IDictionary<string, object>` ve OWIN tarafından gerekli bir kabul eder.</span><span class="sxs-lookup"><span data-stu-id="941fc-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="941fc-123">Aşağıdaki kod, `OwinHello` `UseOwin` uzantı yöntemiyle ASP.NET Core ardışık hattına ara yazılımın (yukarıda gösterildiği) nasıl ekleyeceğinigösterir.</span><span class="sxs-lookup"><span data-stu-id="941fc-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="941fc-124">OWIN ardışık ardışık ardışık ardışık içinde gerçekleşecek diğer eylemleri yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="941fc-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="941fc-125">Yanıt başlıkları yalnızca yanıt akışına ilk yazmadan önce değiştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="941fc-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="941fc-126">Performans nedenleriyle birden çok arama `UseOwin` önerilmez.</span><span class="sxs-lookup"><span data-stu-id="941fc-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="941fc-127">OWIN bileşenleri birlikte gruplandırılırsa en iyi şekilde çalışır.</span><span class="sxs-lookup"><span data-stu-id="941fc-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="941fc-128">OWIN tabanlı bir sunucuda ASP.NET Core Hosting kullanma</span><span class="sxs-lookup"><span data-stu-id="941fc-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="941fc-129">OWIN tabanlı sunucular ASP.NET Core uygulamalarını barındırabilir.</span><span class="sxs-lookup"><span data-stu-id="941fc-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="941fc-130">Böyle bir sunucu [Nowin](https://github.com/Bobris/Nowin), bir .NET OWIN web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="941fc-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="941fc-131">Bu makale için örnekolarak, nowin referansları ve kendi kendine barındırma `IServer` yeteneğine sahip ASP.NET Core oluşturmak için kullanan bir proje dahil ettik.</span><span class="sxs-lookup"><span data-stu-id="941fc-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="941fc-132">`IServer`bir `Features` özellik ve yöntem gerektiren `Start` bir arabirimdir.</span><span class="sxs-lookup"><span data-stu-id="941fc-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="941fc-133">`Start`bu durumda iServerAddressesFeature'dan ayrıştırılan adresleri ayarlayan bir dizi akıcı API çağrısı yla yapılan sunucunun yapılandırılması ve başlatılmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="941fc-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="941fc-134">`_builder` Değişkenin akıcı yapılandırmasının, isteklerin yöntemde `appFunc` daha önce tanımlanan lar tarafından işleneceğini belirttiğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="941fc-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="941fc-135">Bu, `Func` gelen istekleri işlemek için her istek için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="941fc-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="941fc-136">Ayrıca, Nowin `IWebHostBuilder` sunucusunun eklenmesini ve yapılandırılmasını kolaylaştırmak için bir uzantı ekleyeceğiz.</span><span class="sxs-lookup"><span data-stu-id="941fc-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="941fc-137">Bu şekilde, bu özel sunucuyu kullanarak bir ASP.NET Core uygulamasını çalıştırmak için *Program.cs* uzantısını çağırın:</span><span class="sxs-lookup"><span data-stu-id="941fc-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="941fc-138">[ASP.NET Çekirdek Sunucular](xref:fundamentals/servers/index)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="941fc-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="941fc-139">Core'ASP.NET OWIN tabanlı bir sunucuda çalıştırın ve WebSockets desteğini kullanın</span><span class="sxs-lookup"><span data-stu-id="941fc-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="941fc-140">OWIN tabanlı sunucuların özelliklerinin ASP.NET Core tarafından nasıl yararlanılabildiğinin bir başka örneği de WebSockets gibi özelliklere erişimdir.</span><span class="sxs-lookup"><span data-stu-id="941fc-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="941fc-141">Önceki örnekte kullanılan .NET OWIN web sunucusu, yerleşik Web Soketleri için ASP.NET Core uygulaması tarafından yararlanılabilen destek sağlar.</span><span class="sxs-lookup"><span data-stu-id="941fc-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="941fc-142">Aşağıdaki örnekte, Web Soketlerini destekleyen ve WebSockets aracılığıyla sunucuya gönderilen her şeyi yankılayan basit bir web uygulaması gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="941fc-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="941fc-143">Bu [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) öncekiyle aynı `NowinServer` şekilde yapılandırılır - tek fark, uygulamanın `Configure` yönteminde nasıl yapılandırıldığıdır.</span><span class="sxs-lookup"><span data-stu-id="941fc-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="941fc-144">[Basit bir websocket istemcisi](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) kullanarak bir test uygulama gösterir:</span><span class="sxs-lookup"><span data-stu-id="941fc-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Web Soket Test İstemci](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="941fc-146">OWIN ortamı</span><span class="sxs-lookup"><span data-stu-id="941fc-146">OWIN environment</span></span>

<span data-ttu-id="941fc-147">Bir OWIN ortamı nı `HttpContext`kullanarak oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="941fc-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="941fc-148">OWIN tuşları</span><span class="sxs-lookup"><span data-stu-id="941fc-148">OWIN keys</span></span>

<span data-ttu-id="941fc-149">OWIN, http `IDictionary<string,object>` İstek/Yanıt değişimi boyunca bilgi iletişimi kurmak için bir nesneye bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="941fc-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="941fc-150">ASP.NET Core aşağıda listelenen tuşları uygular.</span><span class="sxs-lookup"><span data-stu-id="941fc-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="941fc-151">Birincil [belirtimi, uzantıları](https://owin.org/#spec)ve [OWIN Anahtar Yönergeleri ve Ortak Anahtarlar](https://owin.org/spec/spec/CommonKeys.html)bakın.</span><span class="sxs-lookup"><span data-stu-id="941fc-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="941fc-152">İstek verileri (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="941fc-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="941fc-153">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-153">Key</span></span>               | <span data-ttu-id="941fc-154">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-154">Value (type)</span></span> | <span data-ttu-id="941fc-155">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-156">owin. İstek Şeması</span><span class="sxs-lookup"><span data-stu-id="941fc-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="941fc-157">owin. İstek Yöntemi</span><span class="sxs-lookup"><span data-stu-id="941fc-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="941fc-158">owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="941fc-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="941fc-159">owin. İstek Yolu</span><span class="sxs-lookup"><span data-stu-id="941fc-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="941fc-160">owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="941fc-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="941fc-161">owin. İstek Protokolü</span><span class="sxs-lookup"><span data-stu-id="941fc-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="941fc-162">owin. İstek Headers</span><span class="sxs-lookup"><span data-stu-id="941fc-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="941fc-163">owin. İstek Gövdesi</span><span class="sxs-lookup"><span data-stu-id="941fc-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="941fc-164">İstek verileri (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="941fc-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="941fc-165">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-165">Key</span></span>               | <span data-ttu-id="941fc-166">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-166">Value (type)</span></span> | <span data-ttu-id="941fc-167">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-168">owin. Requestıd</span><span class="sxs-lookup"><span data-stu-id="941fc-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="941fc-169">İsteğe bağlı</span><span class="sxs-lookup"><span data-stu-id="941fc-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="941fc-170">Yanıt verileri (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="941fc-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="941fc-171">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-171">Key</span></span>               | <span data-ttu-id="941fc-172">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-172">Value (type)</span></span> | <span data-ttu-id="941fc-173">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-174">owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="941fc-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="941fc-175">İsteğe bağlı</span><span class="sxs-lookup"><span data-stu-id="941fc-175">Optional</span></span> |
| <span data-ttu-id="941fc-176">owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="941fc-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="941fc-177">İsteğe bağlı</span><span class="sxs-lookup"><span data-stu-id="941fc-177">Optional</span></span> |
| <span data-ttu-id="941fc-178">owin. Responseheaders</span><span class="sxs-lookup"><span data-stu-id="941fc-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="941fc-179">owin. Yanıt Gövdesi</span><span class="sxs-lookup"><span data-stu-id="941fc-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="941fc-180">Diğer veriler (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="941fc-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="941fc-181">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-181">Key</span></span>               | <span data-ttu-id="941fc-182">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-182">Value (type)</span></span> | <span data-ttu-id="941fc-183">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-184">owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="941fc-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="941fc-185">owin. Sürüm</span><span class="sxs-lookup"><span data-stu-id="941fc-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="941fc-186">Ortak tuşlar</span><span class="sxs-lookup"><span data-stu-id="941fc-186">Common keys</span></span>

| <span data-ttu-id="941fc-187">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-187">Key</span></span>               | <span data-ttu-id="941fc-188">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-188">Value (type)</span></span> | <span data-ttu-id="941fc-189">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-190">Ssl. Müşteri Sertifikası</span><span class="sxs-lookup"><span data-stu-id="941fc-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="941fc-191">Ssl. YükClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="941fc-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="941fc-192">Sunucu. Uzaktan Adres</span><span class="sxs-lookup"><span data-stu-id="941fc-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="941fc-193">Sunucu. RemotePort</span><span class="sxs-lookup"><span data-stu-id="941fc-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="941fc-194">Sunucu. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="941fc-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="941fc-195">Sunucu. Yerel Bağlantı Noktası</span><span class="sxs-lookup"><span data-stu-id="941fc-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="941fc-196">Sunucu. IsLocal</span><span class="sxs-lookup"><span data-stu-id="941fc-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="941fc-197">Sunucu. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="941fc-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="941fc-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="941fc-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="941fc-199">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-199">Key</span></span>               | <span data-ttu-id="941fc-200">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-200">Value (type)</span></span> | <span data-ttu-id="941fc-201">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-202">Sendfile. Sendasync</span><span class="sxs-lookup"><span data-stu-id="941fc-202">sendfile.SendAsync</span></span> | <span data-ttu-id="941fc-203">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="941fc-204">İstek Başına</span><span class="sxs-lookup"><span data-stu-id="941fc-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="941fc-205">Opak v0.3.0</span><span class="sxs-lookup"><span data-stu-id="941fc-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="941fc-206">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-206">Key</span></span>               | <span data-ttu-id="941fc-207">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-207">Value (type)</span></span> | <span data-ttu-id="941fc-208">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-209">Opak. Sürüm</span><span class="sxs-lookup"><span data-stu-id="941fc-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="941fc-210">Opak. Yükseltme</span><span class="sxs-lookup"><span data-stu-id="941fc-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="941fc-211">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="941fc-212">Opak. Akışı</span><span class="sxs-lookup"><span data-stu-id="941fc-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="941fc-213">Opak. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="941fc-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="941fc-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="941fc-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="941fc-215">Anahtar</span><span class="sxs-lookup"><span data-stu-id="941fc-215">Key</span></span>               | <span data-ttu-id="941fc-216">Değer (tür)</span><span class="sxs-lookup"><span data-stu-id="941fc-216">Value (type)</span></span> | <span data-ttu-id="941fc-217">Açıklama</span><span class="sxs-lookup"><span data-stu-id="941fc-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="941fc-218">Websocket. Sürüm</span><span class="sxs-lookup"><span data-stu-id="941fc-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="941fc-219">Websocket. Kabul</span><span class="sxs-lookup"><span data-stu-id="941fc-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="941fc-220">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="941fc-221">Websocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="941fc-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="941fc-222">Non-spec</span><span class="sxs-lookup"><span data-stu-id="941fc-222">Non-spec</span></span> |
| <span data-ttu-id="941fc-223">Websocket. Alt Protokol</span><span class="sxs-lookup"><span data-stu-id="941fc-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="941fc-224">Bkz. [RFC6455 Bölüm 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Adım 5.5</span><span class="sxs-lookup"><span data-stu-id="941fc-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="941fc-225">Websocket. Sendasync</span><span class="sxs-lookup"><span data-stu-id="941fc-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="941fc-226">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="941fc-227">Websocket. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="941fc-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="941fc-228">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="941fc-229">Websocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="941fc-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="941fc-230">[Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme</span><span class="sxs-lookup"><span data-stu-id="941fc-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="941fc-231">Websocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="941fc-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="941fc-232">Websocket. Müşteri Kapanış Durumu</span><span class="sxs-lookup"><span data-stu-id="941fc-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="941fc-233">İsteğe bağlı</span><span class="sxs-lookup"><span data-stu-id="941fc-233">Optional</span></span> |
| <span data-ttu-id="941fc-234">Websocket. MüşteriCloseDescription</span><span class="sxs-lookup"><span data-stu-id="941fc-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="941fc-235">İsteğe bağlı</span><span class="sxs-lookup"><span data-stu-id="941fc-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="941fc-236">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="941fc-236">Additional resources</span></span>

* [<span data-ttu-id="941fc-237">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="941fc-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="941fc-238">Sunucular</span><span class="sxs-lookup"><span data-stu-id="941fc-238">Servers</span></span>](xref:fundamentals/servers/index)
