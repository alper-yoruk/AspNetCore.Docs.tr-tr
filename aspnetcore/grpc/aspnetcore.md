---
title: ASP.NET Core içeren gRPC Hizmetleri
author: juntaoluo
description: ASP.NET Core ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/aspnetcore
ms.openlocfilehash: bd97ece1d42b1b90d0b8740e33924d80b91bf49a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016446"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core içeren gRPC Hizmetleri

Bu belgede, ASP.NET Core kullanarak gRPC Hizmetleri ile çalışmaya başlama gösterilmektedir.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core’da gRPC hizmeti ile çalışmaya başlama

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

GRPC projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [GRPC hizmetlerini kullanmaya başlama](xref:tutorials/grpc/grpc-start) .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

`dotnet new grpc -o GrpcGreeter`Komut satırından çalıştırın.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core uygulamasına gRPC Hizmetleri ekleme

gRPC, [GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) paketini gerektirir.

### <a name="configure-grpc"></a>GRPC 'yi yapılandırma

*Startup.cs*içinde:

* gRPC, `AddGrpc` yöntemiyle etkinleştirilir.
* Her gRPC hizmeti, yönlendirme ardışık düzenine yöntemi aracılığıyla eklenir `MapGrpcService` .

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core middlewares ve Özellikler yönlendirme işlem hattını paylaşır, bu nedenle uygulama ek istek işleyicileri sunacak şekilde yapılandırılabilir. MVC denetleyicileri gibi ek istek işleyicileri, yapılandırılmış gRPC hizmetleriyle paralel olarak çalışır.

### <a name="configure-kestrel"></a>Kestrel yapılandırma

Kestrel gRPC uç noktaları:

* HTTP/2 gerektir.
* [Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvenli hale getirilmesi gerekir.

#### <a name="http2"></a>HTTP/2

gRPC, HTTP/2 gerektirir. ASP.NET Core için gRPC, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) olduğunu `HTTP/2` doğrular.

Kestrel çoğu modern işletim sisteminde [http/2 destekler](xref:fundamentals/servers/kestrel#http2-support) . Kestrel uç noktaları, varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.

#### <a name="tls"></a>TLS

GRPC için kullanılan Kestrel uç noktaları TLS ile güvenli hale gelmelidir. Geliştirme aşamasında, `https://localhost:5001` ASP.NET Core geliştirme sertifikası mevcut olduğunda, TLS ile güvenli bir uç nokta otomatik olarak oluşturulur. Yapılandırma gerekmez. `https`Ön ek, Kestrel uç NOKTASıNıN TLS kullandığını doğrular.

Üretimde, TLS açıkça yapılandırılmalıdır. Aşağıdaki *appsettings.js* örnekte, TLS ile güvenliği SAĞLANMıŞ bir http/2 uç noktası verilmiştir:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternatif olarak, Kestrel uç noktaları *program.cs*içinde yapılandırılabilir:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Protokol anlaşması

TLS, iletişimin güvenliğinin daha fazlası için kullanılır. TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir uç nokta birden çok protokolü desteklediğinde istemci ile sunucu arasındaki bağlantı protokolünü anlaşmak için kullanılır. Bu anlaşma, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmadığını belirler.

Bir HTTP/2 uç noktası TLS olmadan yapılandırılırsa, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) olarak ayarlanmalıdır `HttpProtocols.Http2` . Bir anlaşma olmadığından, birden fazla protokolle (örneğin,) bir uç nokta `HttpProtocols.Http1AndHttp2` TLS olmadan kullanılamaz. Güvenli olmayan uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.

HTTP/2 ve TLS 'yi Kestrel ile etkinleştirme hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez. MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir. Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Core API 'Leri ile tümleştirme

gRPC Hizmetleri, [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) ve [günlüğe kaydetme](xref:fundamentals/logging/index)gibi ASP.NET Core özelliklerine tam erişime sahiptir. Örneğin, hizmet uygulama, Oluşturucu aracılığıyla bir günlükçü hizmetini dı kapsayıcısından çözümleyebilir:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Varsayılan olarak, gRPC hizmeti uygulama diğer dı hizmetlerini herhangi bir yaşam süresi (tek, kapsamlı veya geçici) ile çözümleyebilir.

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC yöntemlerinde HttpContext 'i çözümle

GRPC API 'SI, yöntem, ana bilgisayar, üst bilgi ve tanıtımları gibi bazı HTTP/2 ileti verilerine erişim sağlar. Erişim, `ServerCallContext` her gRPC yöntemine geçirilen bağımsız değişkendir:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext``HttpContext`tüm ASP.NET API 'lerinde tam erişim sağlamaz. `GetHttpContext`Genişletme yöntemi, `HttpContext` ASP.NET API 'lerinde temel alınan http/2 iletisini temsil eden öğesine tam erişim sağlar:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
