---
title: ASP.NET Core içeren gRPC Hizmetleri
author: juntaoluo
description: ASP.NET Core ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/aspnetcore
ms.openlocfilehash: c14ae1fb3c2e046ae577c63824eebb4411a6e804
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776226"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="9b417-103">ASP.NET Core içeren gRPC Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="9b417-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="9b417-104">Bu belgede, ASP.NET Core kullanarak gRPC Hizmetleri ile çalışmaya başlama gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="9b417-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b417-105">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="9b417-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b417-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b417-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9b417-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9b417-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9b417-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b417-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="9b417-109">ASP.NET Core’da gRPC hizmeti ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="9b417-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="9b417-110">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9b417-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9b417-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b417-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9b417-112">GRPC projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [GRPC hizmetlerini kullanmaya başlama](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="9b417-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9b417-113">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9b417-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9b417-114">Komut `dotnet new grpc -o GrpcGreeter` satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9b417-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="9b417-115">ASP.NET Core uygulamasına gRPC Hizmetleri ekleme</span><span class="sxs-lookup"><span data-stu-id="9b417-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="9b417-116">gRPC, [GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9b417-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="9b417-117">GRPC 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9b417-117">Configure gRPC</span></span>

<span data-ttu-id="9b417-118">*Startup.cs*içinde:</span><span class="sxs-lookup"><span data-stu-id="9b417-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="9b417-119">gRPC, `AddGrpc` yöntemiyle etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="9b417-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="9b417-120">Her gRPC hizmeti, yönlendirme ardışık düzenine `MapGrpcService` yöntemi aracılığıyla eklenir.</span><span class="sxs-lookup"><span data-stu-id="9b417-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="9b417-121">ASP.NET Core middlewares ve Özellikler yönlendirme işlem hattını paylaşır, bu nedenle uygulama ek istek işleyicileri sunacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="9b417-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="9b417-122">MVC denetleyicileri gibi ek istek işleyicileri, yapılandırılmış gRPC hizmetleriyle paralel olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="9b417-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="9b417-123">Kestrel yapılandırma</span><span class="sxs-lookup"><span data-stu-id="9b417-123">Configure Kestrel</span></span>

<span data-ttu-id="9b417-124">Kestrel gRPC uç noktaları:</span><span class="sxs-lookup"><span data-stu-id="9b417-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="9b417-125">HTTP/2 gerektir.</span><span class="sxs-lookup"><span data-stu-id="9b417-125">Require HTTP/2.</span></span>
* <span data-ttu-id="9b417-126">[Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvenli hale getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="9b417-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="9b417-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9b417-127">HTTP/2</span></span>

<span data-ttu-id="9b417-128">gRPC, HTTP/2 gerektirir.</span><span class="sxs-lookup"><span data-stu-id="9b417-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="9b417-129">ASP.NET Core için gRPC, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) olduğunu `HTTP/2`doğrular.</span><span class="sxs-lookup"><span data-stu-id="9b417-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="9b417-130">Kestrel çoğu modern işletim sisteminde [http/2 destekler](xref:fundamentals/servers/kestrel#http2-support) .</span><span class="sxs-lookup"><span data-stu-id="9b417-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="9b417-131">Kestrel uç noktaları, varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="9b417-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="9b417-132">TLS</span><span class="sxs-lookup"><span data-stu-id="9b417-132">TLS</span></span>

<span data-ttu-id="9b417-133">GRPC için kullanılan Kestrel uç noktaları TLS ile güvenli hale gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="9b417-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="9b417-134">Geliştirme aşamasında, ASP.NET Core geliştirme sertifikası mevcut `https://localhost:5001` olduğunda, TLS ile güvenli bir uç nokta otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="9b417-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="9b417-135">Yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="9b417-135">No configuration is required.</span></span> <span data-ttu-id="9b417-136">`https` Ön ek, Kestrel uç noktasının TLS kullandığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="9b417-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="9b417-137">Üretimde, TLS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9b417-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="9b417-138">Aşağıdaki *appSettings. JSON* ÖRNEĞINDE, TLS ile güvenliği SAĞLANMıŞ bir http/2 uç noktası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9b417-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="9b417-139">Alternatif olarak, Kestrel uç noktaları *program.cs*içinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="9b417-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="9b417-140">Protokol anlaşması</span><span class="sxs-lookup"><span data-stu-id="9b417-140">Protocol negotiation</span></span>

<span data-ttu-id="9b417-141">TLS, iletişimin güvenliğinin daha fazlası için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b417-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="9b417-142">TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir uç nokta birden çok protokolü desteklediğinde istemci ile sunucu arasındaki bağlantı protokolünü anlaşmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b417-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="9b417-143">Bu anlaşma, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="9b417-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="9b417-144">Bir HTTP/2 uç noktası TLS olmadan yapılandırılırsa, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) olarak `HttpProtocols.Http2`ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9b417-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="9b417-145">Bir anlaşma olmadığından, birden fazla protokolle ( `HttpProtocols.Http1AndHttp2`Örneğin,) bir uç nokta TLS olmadan kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="9b417-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="9b417-146">Güvenli olmayan uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b417-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="9b417-147">HTTP/2 ve TLS 'yi Kestrel ile etkinleştirme hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="9b417-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="9b417-148">macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="9b417-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="9b417-149">MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="9b417-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="9b417-150">Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="9b417-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="9b417-151">ASP.NET Core API 'Leri ile tümleştirme</span><span class="sxs-lookup"><span data-stu-id="9b417-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="9b417-152">gRPC Hizmetleri, [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) ve [günlüğe kaydetme](xref:fundamentals/logging/index)gibi ASP.NET Core özelliklerine tam erişime sahiptir.</span><span class="sxs-lookup"><span data-stu-id="9b417-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9b417-153">Örneğin, hizmet uygulama, Oluşturucu aracılığıyla bir günlükçü hizmetini dı kapsayıcısından çözümleyebilir:</span><span class="sxs-lookup"><span data-stu-id="9b417-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="9b417-154">Varsayılan olarak, gRPC hizmeti uygulama diğer dı hizmetlerini herhangi bir yaşam süresi (tek, kapsamlı veya geçici) ile çözümleyebilir.</span><span class="sxs-lookup"><span data-stu-id="9b417-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="9b417-155">GRPC yöntemlerinde HttpContext 'i çözümle</span><span class="sxs-lookup"><span data-stu-id="9b417-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="9b417-156">GRPC API 'SI, yöntem, ana bilgisayar, üst bilgi ve tanıtımları gibi bazı HTTP/2 ileti verilerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="9b417-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="9b417-157">Erişim, her gRPC yöntemine geçirilen `ServerCallContext` bağımsız değişkendir:</span><span class="sxs-lookup"><span data-stu-id="9b417-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="9b417-158">`ServerCallContext`tüm ASP.NET API 'Lerinde tam erişim `HttpContext` sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="9b417-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="9b417-159">`GetHttpContext` Genişletme yöntemi, ASP.NET API 'lerinde temel alınan `HttpContext` http/2 iletisini temsil eden öğesine tam erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="9b417-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="9b417-160">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9b417-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
