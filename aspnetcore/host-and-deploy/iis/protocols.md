---
title: IIS 'de HTTP/2 ile ASP.NET Core kullanma
author: rick-anderson
description: IIS ile HTTP/2 özelliklerini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654379"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="d9a34-103">IIS 'de HTTP/2 ile ASP.NET Core kullanma</span><span class="sxs-lookup"><span data-stu-id="d9a34-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="d9a34-104">, [Kotin Kotalik](https://github.com/jkotalik) tarafından</span><span class="sxs-lookup"><span data-stu-id="d9a34-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d9a34-105">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="d9a34-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="d9a34-106">Windows Server 2016 veya üzeri/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d9a34-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="d9a34-107">IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d9a34-107">IIS 10 or later</span></span>
* <span data-ttu-id="d9a34-108">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="d9a34-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="d9a34-109">[İşlem dışı barındırırken](xref:host-and-deploy/iis/index#out-of-process-hosting-model): genel kullanıma yönelik uç sunucu bağlantıları http/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="d9a34-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="d9a34-110">Bir HTTP/2 bağlantısı oluşturulduğunda, işlem içi dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d9a34-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="d9a34-111">Bir HTTP/2 bağlantısı oluşturulduğunda, işlem dışı bir dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="d9a34-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="d9a34-112">İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..</span><span class="sxs-lookup"><span data-stu-id="d9a34-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="d9a34-113">HTTP/2, HTTPS/TLS bağlantıları için varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="d9a34-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="d9a34-114">HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="d9a34-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="d9a34-115">IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="d9a34-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="d9a34-116">GRPC 'yi desteklemeye yönelik gelişmiş HTTP/2 özellikleri</span><span class="sxs-lookup"><span data-stu-id="d9a34-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="d9a34-117">IIS 'deki ek HTTP/2 özellikleri, yanıt fragmanları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="d9a34-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="d9a34-118">IIS üzerinde gRPC çalıştırmak için gereksinimler:</span><span class="sxs-lookup"><span data-stu-id="d9a34-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="d9a34-119">İşlem içi barındırma.</span><span class="sxs-lookup"><span data-stu-id="d9a34-119">In-process hosting.</span></span>
* <span data-ttu-id="d9a34-120">Windows 10, OS Build 20300,1000 veya üzeri.</span><span class="sxs-lookup"><span data-stu-id="d9a34-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="d9a34-121">Windows Insider Derlemeleriyle kullanılması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="d9a34-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="d9a34-122">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="d9a34-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="d9a34-123">Larına</span><span class="sxs-lookup"><span data-stu-id="d9a34-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="d9a34-124">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="d9a34-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
