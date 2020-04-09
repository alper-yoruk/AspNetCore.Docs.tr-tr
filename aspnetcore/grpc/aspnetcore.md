---
title: ASP.NET Core içeren gRPC Hizmetleri
author: juntaoluo
description: ASP.NET Core ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667631"
---
# <a name="grpc-services-with-aspnet-core"></a>ASP.NET Core içeren gRPC Hizmetleri

Bu belge, ASP.NET Core kullanarak gRPC hizmetlerine nasıl başlanınan bu belgeyi gösterir.

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>ASP.NET Core’da gRPC hizmeti ile çalışmaya başlama

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir).](xref:index#how-to-download-a-sample)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bkz. gRPC projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [gRPC hizmetlerine başlayın.](xref:tutorials/grpc/grpc-start)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Komut `dotnet new grpc -o GrpcGreeter` satırından çalıştırın.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>ASP.NET Core uygulamasına gRPC hizmetleri ekleme

gRPC [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) paketi gerektirir.

### <a name="configure-grpc"></a>gRPC'yi yapılandır

*Startup.cs:*

* yöntemle `AddGrpc` gRPC etkindir.
* Her gRPC hizmeti yöntem aracılığıyla yönlendirme boru `MapGrpcService` hattına eklenir.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core ara yazılımları ve özellikleri yönlendirme ardışık hattını paylaşır, bu nedenle bir uygulama ek istek işleyicilerine hizmet etmek üzere yapılandırılabilir. MVC denetleyicileri gibi ek istek işleyicileri, yapılandırılan gRPC hizmetlerine paralel olarak çalışır.

### <a name="configure-kestrel"></a>Kerkenezi Yapılandır

Kerkenez gRPC uç noktaları:

* HTTP/2'yi talep edin.
* [Taşıma Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvence altına alınmalıdır.

#### <a name="http2"></a>HTTP/2

gRPC http/2 gerektirir. ASP.NET Core için gRPC [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) doğrular. `HTTP/2`

Kerkenez, en modern işletim sistemlerinde [HTTP/2'yi destekler.](xref:fundamentals/servers/kestrel#http2-support) Kestrel uç noktaları varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.

#### <a name="tls"></a>TLS

gRPC için kullanılan kerkenez uç noktaları TLS ile güvence altına alınmalıdır. Geliştirme aşamasında, ASP.NET Çekirdek geliştirme sertifikası nın bulunduğu `https://localhost:5001` anda TLS ile güvenli bir uç nokta otomatik olarak oluşturulur. Yapılandırma gerekmez. Bir `https` önek, Kestrel bitiş noktasının TLS kullandığını doğrular.

Üretimde TLS açıkça yapılandırılmalıdır. Aşağıdaki *appsettings.json* örneğinde, TLS ile güvenli bir HTTP/2 bitiş noktası sağlanır:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternatif olarak, Kerkenez uç noktaları *Program.cs*olarak yapılandırılabilir:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a>Protokol anlaşması

TLS, iletişimi güvence altına almaktan daha fazlası için kullanılır. TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir bitiş noktası birden çok protokolü desteklediğinde istemci ve sunucu arasındaki bağlantı iletişimini görüşmek için kullanılır. Bu görüşme, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmayacağını belirler.

BIR HTTP/2 bitiş noktası TLS olmadan yapılandırılırsa, bitiş noktasının `HttpProtocols.Http2` [ListenOptions.Protocols'u](xref:fundamentals/servers/kestrel#listenoptionsprotocols) . Birden çok protokolü olan bir bitiş `HttpProtocols.Http1AndHttp2`noktası (örneğin, ) tls olmadan kullanılamaz, çünkü anlaşma yoktur. Güvenli olmayan uç nokta varsayılanına yapılan tüm bağlantılar HTTP/1.1 ve gRPC çağrıları başarısız olur.

Kestrel ile HTTP/2 ve TLS'yi etkinleştirme hakkında daha fazla bilgi için [Kerkenez uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)na bakın.

> [!NOTE]
> macOS, TLS ile Core gRPC ASP.NET desteklemez. macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir. Daha fazla bilgi için bkz: [macOS'ta Core gRPC ASP.NET başlatılamıyor.](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)

## <a name="integration-with-aspnet-core-apis"></a>ASP.NET Çekirdek API'lerle entegrasyon

gRPC hizmetleri [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) ve [Günlük](xref:fundamentals/logging/index)gibi ASP.NET Core özelliklerine tam erişime sahiptir. Örneğin, hizmet uygulaması, oluşturucu aracılığıyla DI kapsayıcısından bir logger hizmetini çözebilir:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Varsayılan olarak, gRPC hizmeti uygulaması diğer DI hizmetlerini herhangi bir yaşam süresiyle (Singleton, Scoped veya Geçici) çözebilir.

### <a name="resolve-httpcontext-in-grpc-methods"></a>GRPC yöntemleriyle HttpContext'ı Çözümle

gRPC API yöntem, ana bilgisayar, üstbilgi ve römorklar gibi bazı HTTP/2 ileti verilerine erişim sağlar. Erişim, her `ServerCallContext` gRPC yöntemine geçirilen bağımsız değişkenden geçer:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext`tüm ASP.NET `HttpContext` API'lerine tam erişim sağlamaz. Uzantı yöntemi, `GetHttpContext` ASP.NET `HttpContext` API'lerinde temel http/2 iletisini temsil eden tam erişim sağlar:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
