---
title: ASP.NET Core Kestrel Web sunucusu ile HTTP/2 kullanın
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel ile HTTP/2 kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253985"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="0d0d3-103">ASP.NET Core Kestrel Web sunucusu ile HTTP/2 kullanın</span><span class="sxs-lookup"><span data-stu-id="0d0d3-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="0d0d3-104">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="0d0d3-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="0d0d3-105">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="0d0d3-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="0d0d3-106">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="0d0d3-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="0d0d3-107">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="0d0d3-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="0d0d3-108">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="0d0d3-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="0d0d3-109">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="0d0d3-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="0d0d3-110">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="0d0d3-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="0d0d3-111">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="0d0d3-112">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="0d0d3-113">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="0d0d3-114">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="0d0d3-115">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="0d0d3-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="0d0d3-116">.NET Core 3,0 ile başlayarak, HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="0d0d3-117">Yapılandırma hakkında daha fazla bilgi için [Kestrel http/2 limitleri](xref:fundamentals/servers/kestrel/options#http2-limits) ve [Listenoptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="0d0d3-118">Gelişmiş HTTP/2 özellikleri</span><span class="sxs-lookup"><span data-stu-id="0d0d3-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="0d0d3-119">Kestrel ' deki ek HTTP/2 özellikleri, yanıt tanıtımları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="0d0d3-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="0d0d3-120">Larına</span><span class="sxs-lookup"><span data-stu-id="0d0d3-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="0d0d3-121">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="0d0d3-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
