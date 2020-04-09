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
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>ASP.NET Core ile .NET (OWIN) için Açık Web Arabirimi

Yazar: [Steve Smith](https://ardalis.com/) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core,.NET (OWIN) için Açık Web Arabirimini destekler. OWIN, web uygulamalarının web sunucularından ayrılmasına izin verir. Ara yazılımın istekleri ve ilişkili yanıtları işlemek için bir ardışık alanda kullanılması için standart bir yol tanımlar. ASP.NET Core uygulamaları ve ara yazılımlar OWIN tabanlı uygulamalar, sunucular ve ara yazılımlarla birlikte çalışabilir.

OWIN, birbirinden farklı nesne modellerine sahip iki çerçevenin birlikte kullanılmasını sağlayan bir ayrıştırma katmanı sağlar. Paket `Microsoft.AspNetCore.Owin` iki bağdaştırıcı uygulaması sağlar:

* ASP.NET Core OWIN için 
* OWIN ASP.NET Çekirdek için

Bu, ASP.NET Core'un OWIN uyumlu bir sunucunun/ana bilgisayarın üzerinde barındırılmasına veya diğer OWIN uyumlu bileşenlerin ASP.NET Core'un üzerinde çalıştırılmasına olanak tanır.

> [!NOTE]
> Bu bağdaştırıcıları kullanmak bir performans maliyetiyle birlikte gelir. Yalnızca ASP.NET Core bileşenlerini kullanan `Microsoft.AspNetCore.Owin` uygulamalar paketi veya bağdaştırıcıları kullanmamalıdır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>ASP.NET Core boru hattında OWIN ara yazılımının çalıştırıl

ASP.NET Core'un OWIN desteği `Microsoft.AspNetCore.Owin` paketin bir parçası olarak dağıtılır. Bu paketi yükleyerek Projenize OWIN desteği alabilirsiniz.

OWIN ara yazılım, arabirim `Func<IDictionary<string, object>, Task>` gerektiren [OWIN belirtimine](https://owin.org/spec/spec/owin-1.0.0.html)uygundur ve belirli `owin.ResponseBody`tuşlar (örneğin) ayarlanır. Aşağıdaki basit OWIN middleware görüntüler "Hello World":

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

Örnek imza a `Task` döndürür `IDictionary<string, object>` ve OWIN tarafından gerekli bir kabul eder.

Aşağıdaki kod, `OwinHello` `UseOwin` uzantı yöntemiyle ASP.NET Core ardışık hattına ara yazılımın (yukarıda gösterildiği) nasıl ekleyeceğinigösterir.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

OWIN ardışık ardışık ardışık ardışık içinde gerçekleşecek diğer eylemleri yapılandırabilirsiniz.

> [!NOTE]
> Yanıt başlıkları yalnızca yanıt akışına ilk yazmadan önce değiştirilmelidir.

> [!NOTE]
> Performans nedenleriyle birden çok arama `UseOwin` önerilmez. OWIN bileşenleri birlikte gruplandırılırsa en iyi şekilde çalışır.

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>OWIN tabanlı bir sunucuda ASP.NET Core Hosting kullanma

OWIN tabanlı sunucular ASP.NET Core uygulamalarını barındırabilir. Böyle bir sunucu [Nowin](https://github.com/Bobris/Nowin), bir .NET OWIN web sunucusudur. Bu makale için örnekolarak, nowin referansları ve kendi kendine barındırma `IServer` yeteneğine sahip ASP.NET Core oluşturmak için kullanan bir proje dahil ettik.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`bir `Features` özellik ve yöntem gerektiren `Start` bir arabirimdir.

`Start`bu durumda iServerAddressesFeature'dan ayrıştırılan adresleri ayarlayan bir dizi akıcı API çağrısı yla yapılan sunucunun yapılandırılması ve başlatılmasından sorumludur. `_builder` Değişkenin akıcı yapılandırmasının, isteklerin yöntemde `appFunc` daha önce tanımlanan lar tarafından işleneceğini belirttiğini unutmayın. Bu, `Func` gelen istekleri işlemek için her istek için çağrılır.

Ayrıca, Nowin `IWebHostBuilder` sunucusunun eklenmesini ve yapılandırılmasını kolaylaştırmak için bir uzantı ekleyeceğiz.

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

Bu şekilde, bu özel sunucuyu kullanarak bir ASP.NET Core uygulamasını çalıştırmak için *Program.cs* uzantısını çağırın:

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

[ASP.NET Çekirdek Sunucular](xref:fundamentals/servers/index)hakkında daha fazla bilgi edinin.

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Core'ASP.NET OWIN tabanlı bir sunucuda çalıştırın ve WebSockets desteğini kullanın

OWIN tabanlı sunucuların özelliklerinin ASP.NET Core tarafından nasıl yararlanılabildiğinin bir başka örneği de WebSockets gibi özelliklere erişimdir. Önceki örnekte kullanılan .NET OWIN web sunucusu, yerleşik Web Soketleri için ASP.NET Core uygulaması tarafından yararlanılabilen destek sağlar. Aşağıdaki örnekte, Web Soketlerini destekleyen ve WebSockets aracılığıyla sunucuya gönderilen her şeyi yankılayan basit bir web uygulaması gösterilmektedir.

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

Bu [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) öncekiyle aynı `NowinServer` şekilde yapılandırılır - tek fark, uygulamanın `Configure` yönteminde nasıl yapılandırıldığıdır. [Basit bir websocket istemcisi](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) kullanarak bir test uygulama gösterir:

![Web Soket Test İstemci](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>OWIN ortamı

Bir OWIN ortamı nı `HttpContext`kullanarak oluşturabilirsiniz.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN tuşları

OWIN, http `IDictionary<string,object>` İstek/Yanıt değişimi boyunca bilgi iletişimi kurmak için bir nesneye bağlıdır. ASP.NET Core aşağıda listelenen tuşları uygular. Birincil [belirtimi, uzantıları](https://owin.org/#spec)ve [OWIN Anahtar Yönergeleri ve Ortak Anahtarlar](https://owin.org/spec/spec/CommonKeys.html)bakın.

### <a name="request-data-owin-v100"></a>İstek verileri (OWIN v1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owin. İstek Şeması | `String` |  |
| owin. İstek Yöntemi  | `String` | |    
| owin. RequestPathBase  | `String` | |    
| owin. İstek Yolu | `String` | |     
| owin. RequestQueryString  | `String` | |    
| owin. İstek Protokolü  | `String` | |    
| owin. İstek Headers | `IDictionary<string,string[]>`  | |
| owin. İstek Gövdesi | `Stream`  | |

### <a name="request-data-owin-v110"></a>İstek verileri (OWIN v1.1.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owin. Requestıd | `String` | İsteğe bağlı |

### <a name="response-data-owin-v100"></a>Yanıt verileri (OWIN v1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owin. ResponseStatusCode | `int` | İsteğe bağlı |
| owin. ResponseReasonPhrase | `String` | İsteğe bağlı |
| owin. Responseheaders | `IDictionary<string,string[]>`  | |
| owin. Yanıt Gövdesi | `Stream`  | |

### <a name="other-data-owin-v100"></a>Diğer veriler (OWIN v1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owin. CallCancelled | `CancellationToken` |  |
| owin. Sürüm  | `String` | |   

### <a name="common-keys"></a>Ortak tuşlar

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| Ssl. Müşteri Sertifikası | `X509Certificate` |  |
| Ssl. YükClientCertAsync  | `Func<Task>` | |    
| Sunucu. Uzaktan Adres  | `String` | |    
| Sunucu. RemotePort | `String` | |     
| Sunucu. LocalIpAddress  | `String` | |    
| Sunucu. Yerel Bağlantı Noktası  | `String` | |    
| Sunucu. IsLocal  | `bool` | |    
| Sunucu. OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| Sendfile. Sendasync | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme | İstek Başına |

### <a name="opaque-v030"></a>Opak v0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| Opak. Sürüm | `String` |  |
| Opak. Yükseltme | `OpaqueUpgrade` | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme |
| Opak. Akışı | `Stream` |  |
| Opak. CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| Websocket. Sürüm | `String` |  |
| Websocket. Kabul | `WebSocketAccept` | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme |
| Websocket. AcceptAlt |  | Non-spec |
| Websocket. Alt Protokol | `String` | Bkz. [RFC6455 Bölüm 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Adım 5.5 |
| Websocket. Sendasync | `WebSocketSendAsync` | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme  |
| Websocket. ReceiveAsync | `WebSocketReceiveAsync` | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme  |
| Websocket. CloseAsync | `WebSocketCloseAsync` | [Temsilci imzasını](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) görme  |
| Websocket. CallCancelled | `CancellationToken` |  |
| Websocket. Müşteri Kapanış Durumu | `int` | İsteğe bağlı |
| Websocket. MüşteriCloseDescription | `String` | İsteğe bağlı |

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [Sunucular](xref:fundamentals/servers/index)
