---
title: ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın
author: ardalis
description: ASP.NET Core, Web uygulamalarının Web sunucularından ayrılmasıyla, .NET için açık Web arabirimi 'ni (OWıN) nasıl desteklediğini öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 817eb652f4feedf19dd60873b480917c320272a3
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102763"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>ASP.NET Core ile .NET için Web arabirimi 'ni (OWıN) açın

, [Steve Smith](https://ardalis.com/) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

ASP.NET Core, .NET için açık Web arabirimi 'Ni (OWıN) destekler. OWIN, Web uygulamalarının Web sunucularından ayrılmasıyla izin verir. Bu işlem, ara yazılım için istekleri ve ilişkili yanıtları işlemek üzere bir ardışık düzende kullanılması için standart bir yol tanımlar. ASP.NET Core uygulamalar ve ara yazılım, OWıN tabanlı uygulamalar, sunucular ve ara yazılım ile birlikte çalışabilir.

OWIN, farklı nesne modellerinin birlikte kullanılmasına izin veren bir ayrılmış katman sağlar. `Microsoft.AspNetCore.Owin`Paket iki bağdaştırıcı uygulaması sağlar:

* OWıN 'a ASP.NET Core 
* ASP.NET Core için OWıN

Bu, ASP.NET Core bir OWIN uyumlu sunucu/konak üzerinde barındırılmasına veya diğer OWIN uyumlu bileşenlerin ASP.NET Core üzerinde çalıştırılmasını sağlar.

> [!NOTE]
> Bu bağdaştırıcıların kullanılması bir performans maliyetiyle birlikte gelir. Yalnızca ASP.NET Core bileşenleri kullanan uygulamalar `Microsoft.AspNetCore.Owin` paketi veya bağdaştırıcıları kullanmaz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>ASP.NET Core ardışık düzeninde OWıN ara yazılımı çalıştırma

ASP.NET Core 'nin OWıN desteği paketin bir parçası olarak dağıtılır `Microsoft.AspNetCore.Owin` . Bu paketi yükleyerek, OWıN desteğini projenize aktarabilirsiniz.

OWıN ara yazılımı, bir arabirim gerektiren [owın belirtimine](https://owin.org/spec/spec/owin-1.0.0.html) `Func<IDictionary<string, object>, Task>` ve belirli anahtarların ayarlanmasını sağlar (örneğin, `owin.ResponseBody` ). Aşağıdaki basit OWıN ara yazılımı "Merhaba Dünya" görüntüler:

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

Örnek imza bir döndürür `Task` ve `IDictionary<string, object>` Owin için gereken bir şekilde kabul eder.

Aşağıdaki kod, `OwinHello` genişletme yöntemi ile ASP.NET Core işlem hattına ara yazılım ekleme (yukarıda gösterilen) gösterilmektedir `UseOwin` .

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

OWıN ardışık düzeninde gerçekleşecek diğer eylemleri yapılandırabilirsiniz.

> [!NOTE]
> Yanıt üst bilgileri yalnızca yanıt akışına ilk yazmaya başlamadan önce değiştirilmelidir.

> [!NOTE]
> Performans nedenleriyle birden çok çağrısı `UseOwin` önerilmez. OWIN bileşenleri, birlikte gruplandırılmışsa en iyi şekilde çalışır.

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>OWIN tabanlı bir sunucuda ASP.NET Core çalıştırın ve WebSockets desteğini kullanın

ASP.NET Core ile OWıN tabanlı sunucuların özelliklerinin nasıl yararlanılabilir, WebSockets gibi özelliklere erişim için bir örnektir. Önceki örnekte kullanılan .NET OWIN Web sunucusu, içinde yerleşik olarak bulunan ve bir ASP.NET Core uygulaması tarafından yararlanılabilir olabilen Web Yuvaları desteği içerir. Aşağıdaki örnekte, Web yuvalarını destekleyen ve WebSockets üzerinden sunucuya gönderilen her şeyi yankılayan basit bir Web uygulaması gösterilmektedir.

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

## <a name="owin-environment"></a>OWıN ortamı

Kullanarak bir OWıN ortamı oluşturabilirsiniz `HttpContext` .

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWıN tuşları

OWIN, `IDictionary<string,object>` BIR http isteği/yanıt değişimi boyunca bilgi iletmek için bir nesneye bağımlıdır. ASP.NET Core aşağıda listelenen anahtarları uygular. Bkz. [birincil belirtim, uzantılar](https://owin.org/#spec)ve [Owın anahtar kılavuzları ve ortak anahtarlar](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>İstek verileri (OWıN v 1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owın. RequestScheme | `String` |  |
| owın. RequestMethod  | `String` | |    
| owın. RequestPathBase  | `String` | |    
| owın. Istek yolu | `String` | |     
| owın. RequestQueryString  | `String` | |    
| owın. RequestProtocol  | `String` | |    
| owın. RequestHeaders | `IDictionary<string,string[]>`  | |
| owın. Istek gövdesi | `Stream`  | |

### <a name="request-data-owin-v110"></a>İstek verileri (OWıN v 1.1.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owın. No | `String` | İsteğe Bağlı |

### <a name="response-data-owin-v100"></a>Yanıt verileri (OWIN v 1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owın. ResponseStatusCode | `int` | İsteğe Bağlı |
| owın. ResponseReasonPhrase | `String` | İsteğe Bağlı |
| owın. ResponseHeaders | `IDictionary<string,string[]>`  | |
| owın. Yanıt gövdesi | `Stream`  | |

### <a name="other-data-owin-v100"></a>Diğer veriler (OWIN v 1.0.0)

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| owın. Calliptal edildi | `CancellationToken` |  |
| owın. Sürüm  | `String` | |   

### <a name="common-keys"></a>Ortak anahtarlar

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| SSL. ClientCertificate | `X509Certificate` |  |
| SSL. LoadClientCertAsync  | `Func<Task>` | |    
| Server. Remoteıpaddress  | `String` | |    
| Server. RemotePort | `String` | |     
| Server. LocalIPAddress  | `String` | |    
| Server. LocalPort  | `String` | |    
| Server. IsLocal  | `bool` | |    
| Server. OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v 0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| SendFile. SendAsync | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Istek başına |

### <a name="opaque-v030"></a>Donuk v 0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| ş. Sürüm | `String` |  |
| ş. Yükseltmenizi | `OpaqueUpgrade` | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| ş. Ka | `Stream` |  |
| ş. Calliptal edildi | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v 0.3.0

| Anahtar               | Değer (tür) | Açıklama |
| ----------------- | ------------ | ----------- |
| WebSocket. Sürüm | `String` |  |
| WebSocket. Ettiğinizde | `WebSocketAccept` | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| WebSocket. AcceptAlt |  | Spec dışı |
| WebSocket. Alt protokolü | `String` | Bkz. [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5,5 |
| WebSocket. SendAsync | `WebSocketSendAsync` | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| WebSocket. ReceiveAsync | `WebSocketReceiveAsync` | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| WebSocket. CloseAsync | `WebSocketCloseAsync` | Bkz. [temsilci imzası](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| WebSocket. Calliptal edildi | `CancellationToken` |  |
| WebSocket. ClientCloseStatus | `int` | İsteğe Bağlı |
| WebSocket. ClientCloseDescription | `String` | İsteğe Bağlı |

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [Sunucular](xref:fundamentals/servers/index)
