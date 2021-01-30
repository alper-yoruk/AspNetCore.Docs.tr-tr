---
title: ASP.NET Core içeren gRPC Hizmetleri
author: juntaoluo
description: ASP.NET Core ile gRPC hizmetlerini yazarken temel kavramları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
no-loc:
- appsettings.json
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
uid: grpc/aspnetcore
ms.openlocfilehash: 57edfa31079cb3fca6e9e8d0fa55bcbb8bbfefca
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057492"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="77d4c-103">ASP.NET Core içeren gRPC Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="77d4c-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="77d4c-104">Bu belgede, ASP.NET Core kullanarak gRPC Hizmetleri ile çalışmaya başlama gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="77d4c-105">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="77d4c-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77d4c-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77d4c-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="77d4c-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="77d4c-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77d4c-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77d4c-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="77d4c-109">ASP.NET Core’da gRPC hizmeti ile çalışmaya başlama</span><span class="sxs-lookup"><span data-stu-id="77d4c-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="77d4c-110">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="77d4c-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77d4c-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77d4c-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77d4c-112">GRPC projesi oluşturma hakkında ayrıntılı yönergeler için bkz. [GRPC hizmetlerini kullanmaya başlama](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="77d4c-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="77d4c-113">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77d4c-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="77d4c-114">`dotnet new grpc -o GrpcGreeter`Komut satırından çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="77d4c-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="77d4c-115">ASP.NET Core uygulamasına gRPC Hizmetleri ekleme</span><span class="sxs-lookup"><span data-stu-id="77d4c-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="77d4c-116">gRPC, [GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="77d4c-117">GRPC 'yi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="77d4c-117">Configure gRPC</span></span>

<span data-ttu-id="77d4c-118">*Startup.cs* içinde:</span><span class="sxs-lookup"><span data-stu-id="77d4c-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="77d4c-119">gRPC, `AddGrpc` yöntemiyle etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="77d4c-120">Her gRPC hizmeti, yönlendirme ardışık düzenine yöntemi aracılığıyla eklenir `MapGrpcService` .</span><span class="sxs-lookup"><span data-stu-id="77d4c-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="77d4c-121">ASP.NET Core, işlem hattı ve Özellikler yönlendirme işlem hattını paylaşır, bu nedenle uygulama ek istek işleyicileri sunacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="77d4c-122">MVC denetleyicileri gibi ek istek işleyicileri, yapılandırılmış gRPC hizmetleriyle paralel olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="77d4c-123">Sunucu seçenekleri</span><span class="sxs-lookup"><span data-stu-id="77d4c-123">Server options</span></span>

<span data-ttu-id="77d4c-124">gRPC Hizmetleri, tüm yerleşik ASP.NET Core sunucularıyla barındırılabilir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="77d4c-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="77d4c-125">Kestrel</span></span>
> * <span data-ttu-id="77d4c-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="77d4c-126">TestServer</span></span>
> * <span data-ttu-id="77d4c-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="77d4c-127">IIS&dagger;</span></span>
> * <span data-ttu-id="77d4c-128">HTTP.sys&dagger;</span><span class="sxs-lookup"><span data-stu-id="77d4c-128">HTTP.sys&dagger;</span></span>

<span data-ttu-id="77d4c-129">&dagger;IIS ve HTTP.sys .NET 5 ve Windows 10 derleme 20241 veya üstünü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-129">&dagger;IIS and HTTP.sys require .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="77d4c-130">ASP.NET Core uygulamasının doğru sunucusunu seçme hakkında daha fazla bilgi için, bkz <xref:fundamentals/servers/index> ..</span><span class="sxs-lookup"><span data-stu-id="77d4c-130">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="77d4c-131">Kestrel</span><span class="sxs-lookup"><span data-stu-id="77d4c-131">Kestrel</span></span>

<span data-ttu-id="77d4c-132">[Kestrel](xref:fundamentals/servers/kestrel) , ASP.NET Core için platformlar arası Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-132">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="77d4c-133">Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak bağlantı noktası Paylaşımı gibi HTTP.sys gelişmiş özelliklerden bazılarına sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-133">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="77d4c-134">Kestrel gRPC uç noktaları:</span><span class="sxs-lookup"><span data-stu-id="77d4c-134">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="77d4c-135">HTTP/2 gerektir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-135">Require HTTP/2.</span></span>
* <span data-ttu-id="77d4c-136">[Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvenli hale getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-136">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="77d4c-137">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="77d4c-137">HTTP/2</span></span>

<span data-ttu-id="77d4c-138">gRPC, HTTP/2 gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-138">gRPC requires HTTP/2.</span></span> <span data-ttu-id="77d4c-139">ASP.NET Core için gRPC, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) olduğunu `HTTP/2` doğrular.</span><span class="sxs-lookup"><span data-stu-id="77d4c-139">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="77d4c-140">Kestrel çoğu modern işletim sisteminde [http/2 destekler](xref:fundamentals/servers/kestrel/http2) .</span><span class="sxs-lookup"><span data-stu-id="77d4c-140">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="77d4c-141">Kestrel uç noktaları, varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-141">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="77d4c-142">TLS</span><span class="sxs-lookup"><span data-stu-id="77d4c-142">TLS</span></span>

<span data-ttu-id="77d4c-143">GRPC için kullanılan Kestrel uç noktaları TLS ile güvenli hale gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-143">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="77d4c-144">Geliştirme aşamasında, `https://localhost:5001` ASP.NET Core geliştirme sertifikası mevcut olduğunda, TLS ile güvenli bir uç nokta otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-144">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="77d4c-145">Yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="77d4c-145">No configuration is required.</span></span> <span data-ttu-id="77d4c-146">`https`Ön ek, Kestrel uç NOKTASıNıN TLS kullandığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="77d4c-146">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="77d4c-147">Üretimde, TLS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-147">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="77d4c-148">Aşağıdaki *appsettings.json* örnekte, TLS ile güvenliği sağlanmış BIR http/2 uç noktası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-148">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="77d4c-149">Alternatif olarak, Kestrel uç noktaları *program.cs* içinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-149">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="77d4c-150">Protokol anlaşması</span><span class="sxs-lookup"><span data-stu-id="77d4c-150">Protocol negotiation</span></span>

<span data-ttu-id="77d4c-151">TLS, iletişimin güvenliğinin daha fazlası için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-151">TLS is used for more than securing communication.</span></span> <span data-ttu-id="77d4c-152">TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir uç nokta birden çok protokolü desteklediğinde istemci ile sunucu arasındaki bağlantı protokolünü anlaşmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-152">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="77d4c-153">Bu anlaşma, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="77d4c-153">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="77d4c-154">Bir HTTP/2 uç noktası TLS olmadan yapılandırılırsa, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) olarak ayarlanmalıdır `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="77d4c-154">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="77d4c-155">Birden çok protokolle (örneğin,) bir uç nokta, `HttpProtocols.Http1AndHttp2` hiçbir anlaşma olmadığı IÇIN TLS olmadan kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="77d4c-155">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="77d4c-156">Güvenli olmayan uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-156">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="77d4c-157">HTTP/2 ve TLS 'yi Kestrel ile etkinleştirme hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="77d4c-157">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="77d4c-158">macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="77d4c-158">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="77d4c-159">MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-159">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="77d4c-160">Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="77d4c-160">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="77d4c-161">IIS</span><span class="sxs-lookup"><span data-stu-id="77d4c-161">IIS</span></span>

<span data-ttu-id="77d4c-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) , Web uygulamalarını barındırmak için ASP.NET Core dahil esnek, güvenli ve yönetilebilir bir Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-162">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="77d4c-163">.NET 5 ve Windows 10 Build 20241 veya sonraki sürümleri, gRPC hizmetlerini IIS ile barındırmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-163">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="77d4c-164">IIS, TLS ve HTTP/2 kullanacak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-164">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="77d4c-165">Daha fazla bilgi için bkz. <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="77d4c-165">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="77d4c-166">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="77d4c-166">HTTP.sys</span></span>

<span data-ttu-id="77d4c-167">[HTTP.sys](xref:fundamentals/servers/httpsys) , yalnızca Windows üzerinde çalışan ASP.NET Core için bir Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-167">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="77d4c-168">HTTP.sys ile gRPC hizmetlerini barındırmak için .NET 5 ve Windows 10 Build 20241 veya üzeri gereklidir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-168">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="77d4c-169">HTTP.sys TLS ve HTTP/2 kullanacak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-169">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="77d4c-170">Daha fazla bilgi için bkz.  [ Web sunucusu http/2 desteğiHTTP.sys](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="77d4c-170">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="77d4c-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="77d4c-171">Kestrel</span></span>

<span data-ttu-id="77d4c-172">[Kestrel](xref:fundamentals/servers/kestrel) , ASP.NET Core için platformlar arası Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-172">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="77d4c-173">Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak bağlantı noktası Paylaşımı gibi HTTP.sys gelişmiş özelliklerden bazılarına sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-173">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="77d4c-174">Kestrel gRPC uç noktaları:</span><span class="sxs-lookup"><span data-stu-id="77d4c-174">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="77d4c-175">HTTP/2 gerektir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-175">Require HTTP/2.</span></span>
* <span data-ttu-id="77d4c-176">[Aktarım Katmanı Güvenliği (TLS)](https://tools.ietf.org/html/rfc5246)ile güvenli hale getirilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-176">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="77d4c-177">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="77d4c-177">HTTP/2</span></span>

<span data-ttu-id="77d4c-178">gRPC, HTTP/2 gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-178">gRPC requires HTTP/2.</span></span> <span data-ttu-id="77d4c-179">ASP.NET Core için gRPC, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) olduğunu `HTTP/2` doğrular.</span><span class="sxs-lookup"><span data-stu-id="77d4c-179">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="77d4c-180">Kestrel çoğu modern işletim sisteminde [http/2 destekler](xref:fundamentals/servers/kestrel#http2-support) .</span><span class="sxs-lookup"><span data-stu-id="77d4c-180">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="77d4c-181">Kestrel uç noktaları, varsayılan olarak HTTP/1.1 ve HTTP/2 bağlantılarını destekleyecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-181">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="77d4c-182">TLS</span><span class="sxs-lookup"><span data-stu-id="77d4c-182">TLS</span></span>

<span data-ttu-id="77d4c-183">GRPC için kullanılan Kestrel uç noktaları TLS ile güvenli hale gelmelidir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-183">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="77d4c-184">Geliştirme aşamasında, `https://localhost:5001` ASP.NET Core geliştirme sertifikası mevcut olduğunda, TLS ile güvenli bir uç nokta otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-184">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="77d4c-185">Yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="77d4c-185">No configuration is required.</span></span> <span data-ttu-id="77d4c-186">`https`Ön ek, Kestrel uç NOKTASıNıN TLS kullandığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="77d4c-186">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="77d4c-187">Üretimde, TLS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-187">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="77d4c-188">Aşağıdaki *appsettings.json* örnekte, TLS ile güvenliği sağlanmış BIR http/2 uç noktası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-188">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="77d4c-189">Alternatif olarak, Kestrel uç noktaları *program.cs* içinde yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-189">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="77d4c-190">Protokol anlaşması</span><span class="sxs-lookup"><span data-stu-id="77d4c-190">Protocol negotiation</span></span>

<span data-ttu-id="77d4c-191">TLS, iletişimin güvenliğinin daha fazlası için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-191">TLS is used for more than securing communication.</span></span> <span data-ttu-id="77d4c-192">TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışması, bir uç nokta birden çok protokolü desteklediğinde istemci ile sunucu arasındaki bağlantı protokolünü anlaşmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d4c-192">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="77d4c-193">Bu anlaşma, bağlantının HTTP/1.1 veya HTTP/2 kullanıp kullanmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="77d4c-193">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="77d4c-194">Bir HTTP/2 uç noktası TLS olmadan yapılandırılırsa, uç noktanın [Listenoptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) olarak ayarlanmalıdır `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="77d4c-194">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="77d4c-195">Birden çok protokolle (örneğin,) bir uç nokta, `HttpProtocols.Http1AndHttp2` hiçbir anlaşma olmadığı IÇIN TLS olmadan kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="77d4c-195">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="77d4c-196">Güvenli olmayan uç nokta varsayılan HTTP/1.1 ve gRPC çağrıları için tüm bağlantılar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d4c-196">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="77d4c-197">HTTP/2 ve TLS 'yi Kestrel ile etkinleştirme hakkında daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="77d4c-197">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="77d4c-198">macOS, TLS ile ASP.NET Core gRPC 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="77d4c-198">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="77d4c-199">MacOS 'ta gRPC hizmetlerini başarıyla çalıştırmak için ek yapılandırma gerekir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-199">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="77d4c-200">Daha fazla bilgi için bkz. [macOS üzerinde gRPC uygulaması ASP.NET Core başlatılamıyor](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="77d4c-200">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="77d4c-201">ASP.NET Core API 'Leri ile tümleştirme</span><span class="sxs-lookup"><span data-stu-id="77d4c-201">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="77d4c-202">gRPC Hizmetleri, [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) ve [günlüğe kaydetme](xref:fundamentals/logging/index)gibi ASP.NET Core özelliklerine tam erişime sahiptir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-202">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="77d4c-203">Örneğin, hizmet uygulama, Oluşturucu aracılığıyla bir günlükçü hizmetini dı kapsayıcısından çözümleyebilir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-203">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="77d4c-204">Varsayılan olarak, gRPC hizmeti uygulama diğer dı hizmetlerini herhangi bir yaşam süresi (tek, kapsamlı veya geçici) ile çözümleyebilir.</span><span class="sxs-lookup"><span data-stu-id="77d4c-204">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="77d4c-205">GRPC yöntemlerinde HttpContext 'i çözümle</span><span class="sxs-lookup"><span data-stu-id="77d4c-205">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="77d4c-206">GRPC API 'SI, yöntem, ana bilgisayar, üst bilgi ve tanıtımları gibi bazı HTTP/2 ileti verilerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="77d4c-206">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="77d4c-207">Erişim, `ServerCallContext` her gRPC yöntemine geçirilen bağımsız değişkendir:</span><span class="sxs-lookup"><span data-stu-id="77d4c-207">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="77d4c-208">`ServerCallContext``HttpContext`tüm ASP.NET API 'lerinde tam erişim sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="77d4c-208">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="77d4c-209">`GetHttpContext`Genişletme yöntemi, `HttpContext` ASP.NET API 'lerinde temel alınan http/2 iletisini temsil eden öğesine tam erişim sağlar:</span><span class="sxs-lookup"><span data-stu-id="77d4c-209">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="77d4c-210">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77d4c-210">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
