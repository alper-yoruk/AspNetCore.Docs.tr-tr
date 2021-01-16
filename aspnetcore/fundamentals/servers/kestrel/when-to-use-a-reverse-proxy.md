---
title: ASP.NET Core Kestrel Web sunucusuyla ters proxy kullanma
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel 'in önünde bir ters proxy 'nin ne zaman kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253972"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="fce6e-103">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="fce6e-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="fce6e-104">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="fce6e-105">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="fce6e-106">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fce6e-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](_static/kestrel-to-internet2.png)

<span data-ttu-id="fce6e-108">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="fce6e-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](_static/kestrel-to-internet.png)

<span data-ttu-id="fce6e-110">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="fce6e-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="fce6e-111">Kestrel, ters ara sunucu olmayan bir uç sunucu olarak kullanıldığında, birden çok işlem arasındaki aynı IP adresini ve bağlantı noktasını paylaşma desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fce6e-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="fce6e-112">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="fce6e-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="fce6e-113">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletebilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="fce6e-114">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="fce6e-115">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="fce6e-115">A reverse proxy:</span></span>

* <span data-ttu-id="fce6e-116">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="fce6e-117">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="fce6e-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="fce6e-118">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="fce6e-119">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="fce6e-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="fce6e-120">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="fce6e-121">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](xref:fundamentals/servers/kestrel/host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fce6e-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fce6e-122">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fce6e-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

