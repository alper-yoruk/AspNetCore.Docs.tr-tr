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
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="64bdc-103">ASP.NET Core içeren gRPC Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="64bdc-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="64bdc-104">Bu belge, ASP.NET Core kullanarak gRPC hizmetlerine nasıl başlanınan bu belgeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64bdc-105">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="64bdc-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64bdc-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64bdc-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="64bdc-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64bdc-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="64bdc-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64bdc-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="64bdc-109">ASP.NET Core’da gRPC hizmeti ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="64bdc-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="64bdc-110">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="64bdc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64bdc-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64bdc-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64bdc-112">Bkz. gRPC projesinin nasıl oluşturulacağına ilişkin ayrıntılı talimatlar için [gRPC hizmetlerine başlayın.](xref:tutorials/grpc/grpc-start)</span><span class="sxs-lookup"><span data-stu-id="64bdc-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="64bdc-113">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64bdc-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="64bdc-114">Komut `dotnet new grpc -o GrpcGreeter` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="64bdc-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="64bdc-115">ASP.NET Core uygulamasına gRPC hizmetleri ekleme</span><span class="sxs-lookup"><span data-stu-id="64bdc-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="64bdc-116">gRPC [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) paketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="64bdc-117">gRPC'yi yapılandır</span><span class="sxs-lookup"><span data-stu-id="64bdc-117">Configure gRPC</span></span>

<span data-ttu-id="64bdc-118">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="64bdc-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="64bdc-119">yöntemle `AddGrpc` gRPC etkindir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="64bdc-120">Her gRPC hizmeti yöntem aracılığıyla yönlendirme boru `MapGrpcService` hattına eklenir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="64bdc-121">ASP.NET Core ara yazılımları ve özellikleri yönlendirme ardışık hattını paylaşır, bu nedenle bir uygulama ek istek işleyicilerine hizmet etmek üzere yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="64bdc-122">MVC denetleyicileri gibi ek istek işleyicileri, yapılandırılan gRPC hizmetlerine paralel olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="64bdc-123">Kerkenezi Yapılandır</span><span class="sxs-lookup"><span data-stu-id="64bdc-123">Configure Kestrel</span></span>

<span data-ttu-id="64bdc-124">Kerkenez gRPC uç noktaları:</span><span class="sxs-lookup"><span data-stu-id="64bdc-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="64bdc-125">HTTP/2'yi talep edin.</span><span class="sxs-lookup"><span data-stu-id="64bdc-125">Require HTTP/2.</span></span>
* <span data-ttu-id="64bdc-126">[Taşıma Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvence altına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="64bdc-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="64bdc-127">HTTP/2</span></span>

<span data-ttu-id="64bdc-128">gRPC http/2 gerektirir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="64bdc-129">ASP.NET Core için gRPC [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) doğrular. `HTTP/2`</span><span class="sxs-lookup"><span data-stu-id="64bdc-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="64bdc-130">Kerkenez, en modern işletim sistemlerinde [HTTP/2'yi destekler.](xref:fundamentals/servers/kestrel#http2-support)</span><span class="sxs-lookup"><span data-stu-id="64bdc-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="64bdc-131">Kestrel uç noktaları varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="64bdc-132">TLS</span><span class="sxs-lookup"><span data-stu-id="64bdc-132">TLS</span></span>

<span data-ttu-id="64bdc-133">gRPC için kullanılan kerkenez uç noktaları TLS ile güvence altına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="64bdc-134">Geliştirme aşamasında, ASP.NET Çekirdek geliştirme sertifikası nın bulunduğu `https://localhost:5001` anda TLS ile güvenli bir uç nokta otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="64bdc-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="64bdc-135">Yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="64bdc-135">No configuration is required.</span></span> <span data-ttu-id="64bdc-136">Bir `https` önek, Kestrel bitiş noktasının TLS kullandığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="64bdc-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="64bdc-137">Üretimde TLS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="64bdc-138">Aşağıdaki *appsettings.json* örneğinde, TLS ile güvenli bir HTTP/2 bitiş noktası sağlanır:</span><span class="sxs-lookup"><span data-stu-id="64bdc-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="64bdc-139">Alternatif olarak, Kerkenez uç noktaları *Program.cs*olarak yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="64bdc-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="64bdc-140">Protokol anlaşması</span><span class="sxs-lookup"><span data-stu-id="64bdc-140">Protocol negotiation</span></span>

<span data-ttu-id="64bdc-141">TLS, iletişimi güvence altına almaktan daha fazlası için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="64bdc-142">TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir bitiş noktası birden çok protokolü desteklediğinde istemci ve sunucu arasındaki bağlantı iletişimini görüşmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="64bdc-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="64bdc-143">Bu görüşme, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmayacağını belirler.</span><span class="sxs-lookup"><span data-stu-id="64bdc-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="64bdc-144">BIR HTTP/2 bitiş noktası TLS olmadan yapılandırılırsa, bitiş noktasının `HttpProtocols.Http2` [ListenOptions.Protocols'u](xref:fundamentals/servers/kestrel#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="64bdc-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="64bdc-145">Birden çok protokolü olan bir bitiş `HttpProtocols.Http1AndHttp2`noktası (örneğin, ) tls olmadan kullanılamaz, çünkü anlaşma yoktur.</span><span class="sxs-lookup"><span data-stu-id="64bdc-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="64bdc-146">Güvenli olmayan uç nokta varsayılanına yapılan tüm bağlantılar HTTP/1.1 ve gRPC çağrıları başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="64bdc-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="64bdc-147">Kestrel ile HTTP/2 ve TLS'yi etkinleştirme hakkında daha fazla bilgi için [Kerkenez uç noktası yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration)na bakın.</span><span class="sxs-lookup"><span data-stu-id="64bdc-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="64bdc-148">macOS, TLS ile Core gRPC ASP.NET desteklemez.</span><span class="sxs-lookup"><span data-stu-id="64bdc-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="64bdc-149">macOS'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gereklidir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="64bdc-150">Daha fazla bilgi için bkz: [macOS'ta Core gRPC ASP.NET başlatılamıyor.](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)</span><span class="sxs-lookup"><span data-stu-id="64bdc-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="64bdc-151">ASP.NET Çekirdek API'lerle entegrasyon</span><span class="sxs-lookup"><span data-stu-id="64bdc-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="64bdc-152">gRPC hizmetleri [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) (DI) ve [Günlük](xref:fundamentals/logging/index)gibi ASP.NET Core özelliklerine tam erişime sahiptir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="64bdc-153">Örneğin, hizmet uygulaması, oluşturucu aracılığıyla DI kapsayıcısından bir logger hizmetini çözebilir:</span><span class="sxs-lookup"><span data-stu-id="64bdc-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="64bdc-154">Varsayılan olarak, gRPC hizmeti uygulaması diğer DI hizmetlerini herhangi bir yaşam süresiyle (Singleton, Scoped veya Geçici) çözebilir.</span><span class="sxs-lookup"><span data-stu-id="64bdc-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="64bdc-155">GRPC yöntemleriyle HttpContext'ı Çözümle</span><span class="sxs-lookup"><span data-stu-id="64bdc-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="64bdc-156">gRPC API yöntem, ana bilgisayar, üstbilgi ve römorklar gibi bazı HTTP/2 ileti verilerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="64bdc-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="64bdc-157">Erişim, her `ServerCallContext` gRPC yöntemine geçirilen bağımsız değişkenden geçer:</span><span class="sxs-lookup"><span data-stu-id="64bdc-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="64bdc-158">`ServerCallContext`tüm ASP.NET `HttpContext` API'lerine tam erişim sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="64bdc-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="64bdc-159">Uzantı yöntemi, `GetHttpContext` ASP.NET `HttpContext` API'lerinde temel http/2 iletisini temsil eden tam erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="64bdc-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="64bdc-160">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="64bdc-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
